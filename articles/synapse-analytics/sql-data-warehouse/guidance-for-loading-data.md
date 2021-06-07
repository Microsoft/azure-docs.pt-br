---
title: Práticas recomendadas de carregamento de dados para pools SQL dedicados
description: Recomendações e otimizações de desempenho para carregar dados usando pools de SQL dedicados no Azure Synapse Analytics.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 004084097e2f24a4ceb36f7a016049a574b80b42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594828"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Práticas recomendadas para carregar dados usando pools de SQL dedicados no Azure Synapse Analytics

Neste artigo, você aprenderá as recomendações e otimizações de desempenho para carregar dados usando o pool SQL dedicado.

## <a name="preparing-data-in-azure-storage"></a>Preparando dados no Armazenamento do Azure

Para minimizar a latência, coloque a camada de armazenamento e o pool SQL dedicado.

Ao exportar dados em um formato de arquivo ORC, você poderá receber erros de falta de memória Java quando houver colunas de texto grandes. Para contornar essa limitação, exporte apenas um subconjunto das colunas.

Todos os formatos de arquivo têm características diferentes de desempenho. Para o carregamento mais rápido, use arquivos de texto delimitados compactados. A diferença entre o desempenho de UTF-16 e UTF-8 é mínima.

Dividir arquivos compactados grandes em arquivos compactados menores.

## <a name="running-loads-with-enough-compute"></a>Executando carregamentos com computação suficiente

Para uma velocidade mais alta de carregamento, execute apenas uma carga de trabalho por vez. Se isso não for viável, execute um número mínimo de cargas simultaneamente. Se você espera um trabalho de carregamento grande, considere escalar verticalmente seu pool SQL dedicado antes do carregamento.

Para executar cargas com recursos de computação apropriados, crie usuários de carregamento designados para executar cargas. Classifique cada usuário de carregamento em um grupo de carga de trabalho específico. Para executar uma carga, entre como um dos usuários de carregamento e, em seguida, execute a carga. A carga é executada com o grupo de carga de trabalho do usuário.  

### <a name="example-of-creating-a-loading-user"></a>Exemplo de como criar um usuário de carregamento

Este exemplo cria um usuário de carregamento classificado para um grupo de carga de trabalho específico. A primeira etapa é **conectar-se ao mestre** e criar um logon.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Conecte-se ao pool do SQL dedicado e crie um usuário. O código a seguir pressupõe que você esteja conectado ao banco de dados chamado mySampleDataWarehouse. Ele mostra como criar um usuário chamado Loader e fornece permissões de usuário para criar tabelas e carregar usando a [instrução Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). Em seguida, ele classifica o usuário para o grupo de cargas de trabalho de carregamentos de carga com recursos máximos. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>Este é um exemplo extremo de alocar 100% de recursos do pool do SQL para uma única carga. Isso dará a você uma simultaneidade máxima de 1. Lembre-se de que isso deve ser usado apenas para a carga inicial em que você precisará criar grupos de carga de trabalho adicionais com suas próprias configurações para balanace recursos em suas cargas de trabalho. 

Para executar uma carga com recursos para o grupo de carga de trabalho de carregamento, entre como carregador e execute a carga.

## <a name="allowing-multiple-users-to-load-polybase"></a>Permitindo que vários usuários carreguem (polybase)

Geralmente, é necessário que vários usuários carreguem dados em um pool SQL dedicado. O carregamento com o [CREATE TABLE como SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (polybase) requer permissões de controle do banco de dados.  A permissão CONTROL dá acesso de controle para todos os esquemas.

Talvez você não queira que todos os usuários de carregamento tenham acesso de controle em todos os esquemas. Para limitar as permissões, use a instrução DENY CONTROL.

Por exemplo: considere os esquemas de banco de dados, schema_A para o departamento A e schema_B para o departamento B. Os usuários de banco de dados user_A e user_B serão usuários de carregamento de PolyBase nos departamentos A e B respectivamente. Ambos receberam permissões de banco de dados CONTROL. Os criadores dos esquemas A e B agora bloquearam seus esquemas usando DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A e user_B agora são bloqueados do esquema do outro departamento.

## <a name="loading-to-a-staging-table"></a>Carregando uma tabela de preparo

Para obter a velocidade de carregamento mais rápida para mover dados em uma tabela de pools SQL dedicada, carregue os dados em uma tabela de preparo.  Defina a tabela de preparo como um heap e use o round-robin para a opção de distribuição.

Considere que o carregamento geralmente é um processo de duas etapas no qual você carrega primeiro para uma tabela de preparo e, em seguida, insere os dados em uma tabela de pool de SQL dedicada de produção. Caso a tabela de produção use uma distribuição hash, o tempo total para carregar e inserir pode ser mais rápido se você definir a tabela de preparo com a distribuição de hash.

Carregar para a tabela de preparo demora mais, mas a segunda etapa de inserção das linhas na tabela de produção não incorrerá em movimentação de dados entre as distribuições.

## <a name="loading-to-a-columnstore-index"></a>Carregando para um índice columnstore

Os índices columnstore exigem grandes quantidades de memória para compactar dados em rowgroups de alta qualidade. Para obter a melhor compactação e eficiência de índice, o índice columnstore precisa compactar um máximo de 1.048.576 linhas em cada rowgroup.

Quando há pressão de memória, o índice columnstore pode não ser capaz de alcançar as taxas máximas de compactação. Esse cenário, por sua vez, afeta o desempenho da consulta. Para uma análise profunda, consulte [Otimizações de memória columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Para garantir que o usuário de carregamento tenha memória suficiente para alcançar as taxas máximas de compactação, use usuários de carregamento que sejam membros de uma classe de recursos média ou grande.
- Carregue linhas suficientes para preencher completamente novos rowgroups. Durante um carregamento em massa, a cada 1.048.576 linhas são compactadas diretamente para o columnstore como um rowgroup completo. Carregamentos com menos de 102.400 linhas enviam as linhas para o deltastore, onde as linhas são mantidas em um índice de árvore b.

> [!NOTE]
> Se você carregar poucas linhas, elas poderão ser roteadas para o deltastore e não serão compactadas imediatamente no formato columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Aumentar o tamanho do lote ao usar a API SqLBulkCopy ou o bcp

O carregamento com a instrução de cópia fornecerá a taxa de transferência mais alta com pools SQL dedicados. Se você não pode usar a cópia para carregar e deve usar a [API SqLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ou [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), considere aumentar o tamanho do lote para obter uma melhor taxa de transferência.

> [!TIP]
> Um tamanho de lote entre 100 K a 1M de linhas é a linha de base recomendada para determinar a capacidade de tamanho de lote ideal.

## <a name="handling-loading-failures"></a>Tratamento de falhas de carregamento

Um carregamento usando uma tabela externa pode falhar com o erro *“Consulta anulada – o limite de rejeição máximo foi atingido durante a leitura de uma fonte externa”*. Essa mensagem indica que os dados externos contêm registros sujos.

Um registro de dados é considerado sujo se ele atender a uma das seguintes condições:

- Os tipos de dados e o número de colunas não correspondem às definições de coluna da tabela externa.
- Os dados não estão em conformidade com o formato de arquivo externo especificado.

Para corrigir os registros sujos, verifique se a tabela externa e as definições de formato de arquivo externo estão corretas e se os dados externos são compatíveis com essas definições.

Se um subconjunto de registros de dados externos estiver sujo, você poderá optar por rejeitar esses registros para suas consultas usando as opções de rejeição em [criar tabela externa (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="inserting-data-into-a-production-table"></a>Inserindo dados em uma tabela de produção

Uma única carga para uma pequena tabela com uma [instrução INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), ou mesmo uma recarga periódica de uma pesquisa pode ser boa o suficiente com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Porém, inserções únicas de toneladas não são tão eficientes como executar um carregamento em massa.

Se você tiver milhares ou mais de inserções únicas ao longo do dia, crie lotes para as inserções para que possa carregá-las em massa.  Desenvolva seus processos para acrescentar as inserções únicas para um arquivo e depois crie outro processo que periodicamente carrega o arquivo.

## <a name="creating-statistics-after-the-load"></a>Criando estatísticas após o carregamento

Para melhorar o desempenho de suas consultas, é importante que as estatísticas sejam criadas em todas as colunas de todas as tabelas após o primeiro carregamento ou ocorrem alterações significativas nos dados. A criação de estatísticas pode ser feita manualmente ou você pode habilitar [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

Para obter uma explicação detalhada das estatísticas, confira [Estatísticas](sql-data-warehouse-tables-statistics.md). O exemplo a seguir mostra como criar estatísticas manualmente em cinco colunas da tabela Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Girar chaves de armazenamento (polybase)

É uma boa prática de segurança alterar a chave de acesso para seu armazenamento de blob regularmente. Você tem duas chaves de armazenamento para sua conta de armazenamento de blob, o que permite a transição das chaves.

Para girar chaves de conta de armazenamento do Azure:

Para cada conta de armazenamento cuja chave foi alterada, execute [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Exemplo:

A chave original é criada

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Altere a chave 1 para a chave 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Não é necessária nenhuma outra alteração nas fontes de dados externas subjacentes.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a instrução de cópia ou o polybase ao criar um processo de extração, carregamento e transformação (ELT), consulte [design ELT para o Azure Synapse Analytics](design-elt-data-loading.md).
- Para obter um tutorial de carregamento, [use a instrução Copy para carregar dados do armazenamento de BLOBs do Azure para Synapse SQL](./load-data-from-azure-blob-storage-using-copy.md).
- Para monitorar os carregamentos de dados, consulte [Monitorar sua carga de trabalho usando DMVs](sql-data-warehouse-manage-monitor.md).
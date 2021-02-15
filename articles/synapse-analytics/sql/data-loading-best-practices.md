---
title: Melhores práticas de carregamento de dados
description: Recomendações e otimizações de desempenho para carregar dados em um pool SQL dedicado do Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 31014d336b5122251cf8be4a166520064776fce3
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118159"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-azure-synapse-analytics"></a>Práticas recomendadas para carregar dados em um pool SQL dedicado Azure Synapse Analytics

Neste artigo, você encontrará recomendações e otimizações de desempenho para carregar dados.

## <a name="prepare-data-in-azure-storage"></a>Preparar dados no armazenamento do Azure

Para minimizar a latência, coloque a camada de armazenamento e o pool SQL dedicado.

Ao exportar dados em um formato de arquivo ORC, você poderá receber erros de falta de memória Java quando houver colunas de texto grandes. Para contornar essa limitação, exporte apenas um subconjunto das colunas.

O polybase não pode carregar linhas que tenham mais de 1 milhão bytes de dados. Ao colocar dados nos arquivos de texto no armazenamento de blob do Azure ou do Azure Data Lake Store, eles devem ter menos de 1.000.000 de bytes de dados. Essa limitação de bytes é verdadeira, independentemente do esquema de tabela.

Todos os formatos de arquivo têm características diferentes de desempenho. Para o carregamento mais rápido, use arquivos de texto delimitados compactados. A diferença entre o desempenho de UTF-16 e UTF-8 é mínima.

Dividir arquivos compactados grandes em arquivos compactados menores.

## <a name="run-loads-with-enough-compute"></a>Executar cargas com computação suficiente

Para uma velocidade mais alta de carregamento, execute apenas uma carga de trabalho por vez. Se isso não for possível, execute uma quantidade mínima de carregamento simultaneamente. Se você espera um trabalho de carregamento grande, considere escalar verticalmente seu pool SQL dedicado antes do carregamento.

Para executar cargas com recursos de computação apropriados, crie usuários de carregamento designados para executar cargas. Atribua cada usuário de carregamento a uma classe de recurso ou grupo de carga de trabalho específico. Para executar uma carga, entre como um dos usuários de carregamento e, em seguida, execute a carga. A carga é executada com a classe de recurso do usuário.  Esse método é mais simples do que tentar alterar a classe de recurso do usuário para se ajustar à necessidade de classe de recurso atual.

### <a name="create-a-loading-user"></a>Criar um usuário de carregamento

Este exemplo cria um usuário de carregamento para a classe de recurso staticrc20. A primeira etapa é **conectar-se ao mestre** e criar um logon.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Conecte-se ao data warehouse e crie um usuário. O código a seguir pressupõe que você esteja conectado ao banco de dados chamado mySampleDataWarehouse. Ele mostra como criar um usuário chamado LoaderRC20 e conceder ao usuário a permissão de controle em um banco de dados. Depois, ele adiciona o usuário como membro da função de banco de dados staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Para executar uma carga com recursos para as classes de recursos staticRC20, entre como LoaderRC20 e execute a carga.

Execute cargas sob classes de recursos estáticas em vez de dinâmicas. Usar as classes de recursos estáticas garante os mesmos recursos, independentemente de suas [unidades de data warehouse](resource-consumption-models.md). Se você usar uma classe de recursos dinâmicos, os recursos variam de acordo com seu nível de serviço. Para classes dinâmicas, um nível inferior do serviço significa que você provavelmente precisa usar uma classe de recursos maior para seu usuário de carregamento.

## <a name="allow-multiple-users-to-load"></a>Permitir que vários usuários carreguem

Geralmente, há a necessidade de ter vários usuários carregando dados em um data warehouse. Carregar com [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) requer permissões CONTROL do banco de dados.  A permissão CONTROL dá acesso de controle para todos os esquemas. Talvez você não queira que todos os usuários de carregamento tenham acesso de controle em todos os esquemas. Para limitar as permissões, use a instrução DENY CONTROL.

Por exemplo: considere os esquemas de banco de dados, schema_A para o departamento A e schema_B para o departamento B. Os usuários de banco de dados user_A e user_B serão usuários de carregamento de PolyBase nos departamentos A e B respectivamente. Ambos receberam permissões de banco de dados CONTROL. Os criadores dos esquemas A e B agora bloquearam seus esquemas usando DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A e user_B agora são bloqueados do esquema do outro departamento.

## <a name="load-to-a-staging-table"></a>Carregar para uma tabela de preparo

Para obter a velocidade de carregamento mais rápida para a movimentação de dados em uma tabela de data warehouse, carregue dados em uma tabela de preparo.  Defina a tabela de preparo como um heap e use o round-robin para a opção de distribuição.

Leve em consideração que o carregamento é geralmente um processo de duas etapas, no qual primeiro você faz o carregamento para uma tabela de preparo e depois insere os dados em uma tabela de data warehouse de produção. Caso a tabela de produção use uma distribuição hash, o tempo total para carregar e inserir pode ser mais rápido se você definir a tabela de preparo com a distribuição de hash. Carregar para a tabela de preparo demora mais, mas a segunda etapa de inserção das linhas na tabela de produção não incorrerá em movimentação de dados entre as distribuições.

## <a name="load-to-a-columnstore-index"></a>Carregar para um índice columnstore

Os índices columnstore exigem grandes quantidades de memória para compactar dados em rowgroups de alta qualidade. Para obter a melhor compactação e eficiência de índice, o índice columnstore precisa compactar um máximo de 1.048.576 linhas em cada rowgroup. Quando há pressão de memória, o índice columnstore pode não ser capaz de alcançar as taxas máximas de compactação. Isso afeta o desempenho da consulta. Para uma análise profunda, consulte [Otimizações de memória columnstore](data-load-columnstore-compression.md).

- Para garantir que o usuário de carregamento tenha memória suficiente para alcançar as taxas máximas de compactação, use usuários de carregamento que sejam membros de uma classe de recursos média ou grande.
- Carregue linhas suficientes para preencher completamente novos rowgroups. Durante uma carga em massa, a cada 1.048.576 linhas são compactadas diretamente no columnstore como um rowgroup completo. Carregamentos com menos de 102.400 linhas enviam as linhas para o deltastore, onde as linhas são mantidas em um índice de árvore b. Se você carregar um número muito pequeno de linhas, elas podem ir todas para o deltastore e não serem compactadas imediatamente no formato de columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Aumentar o tamanho do lote ao usar a API SQLBulkCopy ou o BCP

Como mencionado anteriormente, o carregamento com o polybase fornecerá a taxa de transferência mais alta com o pool SQL Synapse. Se você não pode usar o polybase para carregar e deve usar a API SQLBulkCopy (ou BCP), considere aumentar o tamanho do lote para uma melhor taxa de transferência-uma boa regra prática é um tamanho de lote entre 100 mil e 1 milhão de linhas.

## <a name="manage-loading-failures"></a>Gerenciar falhas de carregamento

Um carregamento usando uma tabela externa pode falhar com o erro *“Consulta anulada – o limite de rejeição máximo foi atingido durante a leitura de uma fonte externa”*. Essa mensagem indica que os dados externos contêm registros sujos. Um registro de dados é considerado sujo se os tipos de dados e o número de colunas não correspondem às definições de coluna da tabela externa ou se os dados não são compatíveis com o formato de arquivo externo especificado.

Para corrigir os registros sujos, verifique se a tabela externa e as definições de formato de arquivo externo estão corretas e se os dados externos são compatíveis com essas definições. Caso um subconjunto de registros de dados externos esteja sujo, é possível rejeitar esses registros para suas consultas usando as opções de rejeição em CREATE EXTERNAL TABLE.

## <a name="insert-data-into-a-production-table"></a>Inserir dados em uma tabela de produção

Uma única carga para uma pequena tabela com uma [instrução INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), ou mesmo uma recarga periódica de uma pesquisa pode ser boa o suficiente com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  No entanto, as inserções singleton não são tão eficientes quanto executar uma carga em massa.

Se você tiver milhares ou mais de inserções únicas ao longo do dia, crie lotes para as inserções para que possa carregá-las em massa.  Desenvolva seus processos para acrescentar as inserções únicas para um arquivo e depois crie outro processo que periodicamente carrega o arquivo.

## <a name="create-statistics-after-the-load"></a>Criar estatísticas após o carregamento

Para melhorar o desempenho da consulta, é importante criar estatísticas em todas as colunas de todas as tabelas após a primeira carga ou grandes alterações ocorrerem nos dados. CREATE STATISTICS pode ser feito manualmente ou você pode habilitar as [Estatísticas de criação automática](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Para obter uma explicação detalhada das estatísticas, confira [Estatísticas](develop-tables-statistics.md). O exemplo a seguir mostra como criar estatísticas manualmente em cinco colunas da tabela Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Girar as chaves de armazenamento

É uma boa prática de segurança alterar a chave de acesso para seu armazenamento de blob regularmente. Você tem duas chaves de armazenamento para sua conta de armazenamento de blob, o que permite a transição das chaves.

Para girar chaves de conta de armazenamento do Azure:

Para cada conta de armazenamento cuja chave foi alterada, execute [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

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

- Para saber mais sobre o polybase e criar um processo de extração, carregamento e transformação (ELT), consulte [design ELT for Azure Synapse Analytics](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Para um tutorial de carregamento, [use o polybase para carregar dados do armazenamento de BLOBs do Azure para o Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).
- Para monitorar os carregamentos de dados, consulte [Monitorar sua carga de trabalho usando DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
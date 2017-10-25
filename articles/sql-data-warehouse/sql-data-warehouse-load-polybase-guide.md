---
title: Guia para usar PolyBase no SQL Data Warehouse | Microsoft Docs
description: "Diretrizes e recomendações para usar o PolyBase em cenários do SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 9/13/2017
ms.custom: loading
ms.author: cakarst;barbkess
ms.openlocfilehash: e8ae0eb96200c167a8758df4ce20b51452cc59a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guia para usar o PolyBase no SQL Data Warehouse
Este guia fornece informações práticas para usar o PolyBase no SQL Data Warehouse.

Para começar, consulte o tutorial [Carregar dados com o PolyBase][Load data with PolyBase].

## <a name="rotating-storage-keys"></a>Rotação de chaves de armazenamento
Às vezes você desejará alterar a chave de acesso para o armazenamento de blob por motivos de segurança.

A maneira mais elegante de realizar essa tarefa é seguir um processo conhecido como "rotação das chaves". Você talvez tenha observado que há duas chaves de armazenamento para a conta de armazenamento de blob. Isso é para que você possa fazer a transição

A rotação das suas chaves da conta de Armazenamento do Azure é um processo simples de três etapas

1. Criar uma segunda credencial no escopo do banco de dados com base na chave de acesso de armazenamento secundário
2. Criar a segunda fonte de dados externa com base nessa nova credencial
3. Remover e criar as tabelas externas apontando para a nova fonte de dados externa

Depois de migrar todas as tabelas externas para a nova fonte de dados externa, você pode executar as tarefas de limpeza:

1. Remover a primeira fonte de dados externa
2. Remover a primeira credencial no escopo do banco de dados na chave de acesso de armazenamento primário
3. Fazer logon no Azure e regenerar a chave de acesso primária pronta para a próxima vez



## <a name="load-data-with-external-tables"></a>Carregar dados com Tabelas Externas
Este exemplo carrega dados do armazenamento de blob do Azure no banco de dados do SQL Data Warehouse.

Armazenar dados diretamente elimina o tempo de transferência de dados para consultas. Armazenar dados com um índice columnstore melhora o desempenho de consultas de análise em até 10 vezes.

Este exemplo usa a instrução CREATE TABLE AS SELECT para carregar dados. A nova tabela herda as colunas nomeadas na consulta. Ela herda os tipos de dados dessas colunas da definição de tabela externa.

CREATE TABLE AS SELECT é uma instrução Transact-SQL de alto desempenho que carrega os dados em paralelo em todos os nós de computação do seu SQL Data Warehouse.  Ela foi originalmente desenvolvida para o mecanismo MPP (processamento massivamente paralelo) no Analytics Platform System e agora está no SQL Data Warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Consulte [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)].

> [!NOTE]
> Um carregamento usando uma tabela externa pode falhar com o erro *“Consulta anulada – o limite de rejeição máximo foi atingido durante a leitura de uma fonte externa”*. Isso indica que os dados externos contêm registros *sujos* . Um registro de dados é considerado “sujo” se os tipos de dados/número de colunas reais não correspondem às definições de coluna da tabela externa ou se os dados não são compatíveis com o formato de arquivo externo especificado. Para corrigir esse problema, verifique se a tabela externa e as definições de formato de arquivo externo estão corretas e se os dados externos são compatíveis com essas definições. Caso um subconjunto de registros de dados externos esteja sujo, é possível rejeitar esses registros para suas consultas usando as opções de rejeição em CREATE EXTERNAL TABLE DDL.
> 
> 


## <a name="create-statistics-on-newly-loaded-data"></a>Criar estatísticas sobre os dados recém-carregados
O SQL Data Warehouse do Azure ainda não dá suporte a estatísticas de criação ou atualização automática.  Para obter o melhor desempenho de suas consultas, é importante que as estatísticas sejam criadas em todas as colunas de todas as tabelas após o primeiro carregamento ou após uma alteração significativa nos dados.  Para obter uma explicação detalhada das estatísticas, confira o tópico [Estatísticas][Statistics] no grupo de tópicos Desenvolver.  Veja abaixo um exemplo de como criar estatísticas na tabela carregada neste exemplo.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-with-external-tables"></a>Exportar dados com Tabelas Externas
Esta seção mostra como exportar dados do SQL Data Warehouse para o armazenamento de blobs do Azure usando tabelas externas. Este exemplo usa CREATE EXTERNAL TABLE AS SELECT, que é uma instrução Transact-SQL de alto desempenho, para exportar os dados em paralelo de todos os nós de computação.

O exemplo a seguir cria uma tabela Weblogs2014 externa usando definições de coluna e dados da tabela dbo.Weblogs. A definição da tabela externa é armazenada no SQL Data Warehouse e os resultados da instrução SELECT são exportados para o diretório "/archive/log2014 /" no contêiner de blob especificado pela fonte de dados. Os dados são exportados no formato de arquivo de texto especificado.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```
## <a name="isolate-loading-users"></a>Isolar Usuários em carregamento
Geralmente, há a necessidade de ter vários usuários que podem carregar dados em um SQL DW. Como [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] requer permissões CONTROL do banco de dados, você acabará tendo vários usuários com acesso de controle sobre todos os esquemas. Para limitar isso, você pode usar a instrução DENY CONTROL.

Exemplo: considere os esquemas de banco de dados schema_A para o departamento A e schema_B para o departamento B. Os usuários de banco de dados user_A e user_B serão usuários de carregamento de PolyBase nos departamentos A e B, respectivamente. Ambos receberam permissões de banco de dados CONTROL.
Os criadores dos esquemas A e B agora bloquearam seus esquemas usando DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 Com isso, user_A e user_B devem agora ser bloqueados do esquema do outro departamento.
 
## <a name="polybase-performance-optimizations"></a>Otimizações de desempenho do PolyBase
Para obter o desempenho de carregamento ideal com o PolyBase, sugerimos o seguinte:
- Dividir arquivos compactados grandes em arquivos compactados menores. Os tipos de compactação com suporte no momento não são divisíveis. Como resultado, o desempenho será afetado com o carregamento de um único arquivo grande.
- Para maior velocidade de carregamento, carregue-o em uma tabela de preparo de heap, round_robin. Essa será a maneira mais eficiente para mover os dados da camada de armazenamento para o data warehouse.
- Todos os formatos de arquivo têm características diferentes de desempenho. Para o carregamento mais rápido, use arquivos de texto delimitados compactados. A diferença entre o desempenho de UTF-16 e UTF-8 é mínima.
- Colocalizar a camada de armazenamento e o data warehouse para minimizar a latência
- Aumente o data warehouse se esperar um trabalho de carregamento grande.

## <a name="polybase-limitations"></a>Limitações do PolyBase
O PolyBase no SQL DW tem as seguintes limitações que precisam ser levadas em consideração durante a criação de um trabalho de carregamento:
- Uma única linha não pode ser maior que 1.000.000 bytes. Isso é verdadeiro, independentemente do esquema de tabela definido.
- Ao exportar dados para um Formato de Arquivo ORC do SQL Server ou do SQL Data Warehouse do Azure, as colunas pesadas de texto podem ser limitadas a apenas 50 colunas, devido a erros de memória insuficiente do Java. Para resolver isso, exporte apenas um subconjunto das colunas.





## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como mover dados para o SQL Data Warehouse, consulte o [Visão geral de migração de dados][data migration overview].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->

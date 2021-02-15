---
title: Relatórios entre bancos de dados em nuvem expandidos
description: como configurar consultas elásticas em partições horizontais
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 148c4828309738a18dbda5fd35ea634e8384bfde
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792099"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Relatórios entre bancos de dados em nuvem expandidos (visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Consultar em fragmentos][1]

Bancos de dados fragmentados distribuem linhas em uma camada de dados expandida. O esquema é idêntico em todos os bancos de dados participantes, também conhecido como particionamento horizontal. Usando uma consulta elástica, você pode criar relatórios que abrangem todos os bancos de dados em um banco de dados fragmentado.

Para obter um início rápido, consulte [relatórios entre bancos de dados de nuvem expandidos](elastic-query-getting-started.md).

Para bancos de dados não fragmentados, consulte [Query across cloud databases with different schemas (Consulta entre bancos de dados na nuvem com esquemas diferentes)](elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Crie um mapa de fragmentos usando a biblioteca de cliente do banco de dados elástico. Confira [Gerenciamento de mapa de fragmentos](elastic-scale-shard-map-management.md). Ou use o aplicativo de exemplo em [Introdução às ferramentas do Banco de Dados Elástico](elastic-scale-get-started.md).
* Como alternativa, confira [Migrar bancos de dados existentes para bancos de dados expandidos](elastic-convert-to-use-elastic-tools.md).
* O usuário deve ter a permissão para ALTERAR QUALQUER FONTE DE DADOS EXTERNA. Essa permissão está incluída na permissão ALTERAR BANCO DE DADOS.
* As permissões para ALTERAR QUALQUER FONTE DE DADOS EXTERNA são necessárias para referenciar a fonte de dados subjacente.

## <a name="overview"></a>Visão geral

Essas instruções criam a representação de metadados de sua camada de dados fragmentados no banco de dados de consulta elástico.

1. [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql)
2. [CRIAR UMA CREDENCIAL NO ESCOPO DO BANCO DE DADOS](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Criar chave mestra do escopo do banco de dados e credenciais

A credencial é usada pela consulta elástica para se conectar aos bancos de dados remotos.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Certifique-se de que *" \<username\> "* não inclui nenhum sufixo *" \@ ServerName"* .

## <a name="12-create-external-data-sources"></a>1.2 Criar fontes de dados externas

Sintaxe:

```sql
<External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
        (TYPE = SHARD_MAP_MANAGER,
                   LOCATION = '<fully_qualified_server_name>',
        DATABASE_NAME = ‘<shardmap_database_name>',
        CREDENTIAL = <credential_name>,
        SHARD_MAP_NAME = ‘<shardmapname>’
               ) [;]
```

### <a name="example"></a>Exemplo

```sql
CREATE EXTERNAL DATA SOURCE MyExtSrc
WITH
(
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
);
```

Recupere a lista de fontes de dados externas atuais:

```sql
select * from sys.external_data_sources;
```

A fonte de dados externa faz referência ao seu mapa de fragmentos. Uma consulta elástica então usa a fonte de dados externa e o mapa de fragmentos subjacente para enumerar os bancos de dados que participam da camada de dados.
As mesmas credenciais são usadas para ler o mapa de fragmentos e acessar os dados nos fragmentos durante o processamento de uma consulta elástica.

## <a name="13-create-external-tables"></a>1.3 Criar tabelas externas

Sintaxe:  

```sql
CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
    ( { <column_definition> } [ ,...n ])
    { WITH ( <sharded_external_table_options> ) }
) [;]  

<sharded_external_table_options> ::=
  DATA_SOURCE = <External_Data_Source>,
  [ SCHEMA_NAME = N'nonescaped_schema_name',]
  [ OBJECT_NAME = N'nonescaped_object_name',]
  DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN
```

**Exemplo**

```sql
CREATE EXTERNAL TABLE [dbo].[order_line](
     [ol_o_id] int NOT NULL,
     [ol_d_id] tinyint NOT NULL,
     [ol_w_id] int NOT NULL,
     [ol_number] tinyint NOT NULL,
     [ol_i_id] int NOT NULL,
     [ol_delivery_d] datetime NOT NULL,
     [ol_amount] smallmoney NOT NULL,
     [ol_supply_w_id] int NOT NULL,
     [ol_quantity] smallint NOT NULL,
      [ol_dist_info] char(24) NOT NULL
)

WITH
(
    DATA_SOURCE = MyExtSrc,
     SCHEMA_NAME = 'orders',
     OBJECT_NAME = 'order_details',
    DISTRIBUTION=SHARDED(ol_w_id)
);
```

Recupere a lista de tabelas externas do banco de dados atual:

```sql
SELECT * from sys.external_tables;
```

Para descartar tabelas externas:

```sql
DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]
```

### <a name="remarks"></a>Comentários

A cláusula DATA\_SOURCE define a fonte de dados externa (um mapa de fragmentos) usada para a tabela externa.  

As cláusulas SCHEMA\_NAME e OBJECT\_NAME mapeiam a definição da tabela externa para uma tabela em um esquema diferente. Se for omitido, o esquema do objeto remoto será considerado “dbo” e seu nome será considerado como sendo idêntico ao nome da tabela externa que está sendo definido. Isso é útil se o nome da tabela remota já existe no banco de dados em que você deseja criar a tabela externa. Por exemplo, defina uma tabela externa para obter uma exibição agregada de exibições de catálogo ou de DMVs em sua camada de dados expandida. Como as exibições de catálogo e as DMVs já existem localmente, você não pode usar seus nomes para a definição da tabela externa. Em vez disso, use um nome diferente e use a exibição do catálogo ou o nome de DMV nas cláusulas SCHEMA\_NAME e/ou OBJECT\_NAME. (Veja o exemplo abaixo.)

A cláusula DISTRIBUTION especifica a distribuição de dados usada para esta tabela. O processador de consultas utiliza as informações fornecidas na cláusula DISTRIBUTION para criar planos de consulta mais eficientes.

1. **Fragmentado** significa que os dados são particionados horizontalmente em todos os bancos. A chave de particionamento para a distribuição de dados é o parâmetro **<sharding_column_name>** .
2. **Replicado** significa que cópias idênticas da tabela estão presentes em cada banco de dados. É sua responsabilidade assegurar que as réplicas sejam idênticas entre os bancos de dados.
3. **Redondo \_ ROBIN** significa que a tabela é particionada horizontalmente usando um método de distribuição dependente de aplicativo.

**Referência de camada de dados** : a DDL da tabela externa faz referência a uma fonte de dados externa. A fonte de dados externa especifica um mapa de fragmentos que fornece a tabela externa com as informações necessárias para localizar todos os bancos de dados em sua camada.

### <a name="security-considerations"></a>Considerações de segurança

Usuários com acesso à tabela externa têm acesso automaticamente a tabelas remotas subjacentes com a credencial fornecida na definição de fonte de dados externa. Evite a elevação de privilégios indesejada usando credencial da fonte de dados externa. Use GRANT ou REVOKE para uma tabela externa como se fosse uma tabela normal.  

Depois de definir a fonte de dados externa e as tabelas externas, agora você poderá usar o T-SQL completo nas tabelas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemplo: consultando bancos de dados particionados horizontais

A consulta a seguir executa uma junção de três vias entre depósitos, pedidos e linhas de ordem e usa várias agregações e um filtro seletivo. Ele assume (1) o particionamento horizontal (fragmentação) e (2) que os depósitos, pedidos e linhas de ordem são fragmentados pela coluna ID do depósito e que a consulta elástica pode colocalizar as junções nos fragmentos e processar a parte cara da consulta nos fragmentos em paralelos.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedimento armazenado para a execução remota de T-SQL: sp\_execute_remote

A consulta elástica também apresenta um procedimento armazenado que fornece acesso direto aos fragmentos. O procedimento armazenado é chamado [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) e pode ser usado para executar procedimentos armazenados remotos ou código T-SQL em bancos de dados remotos. Ele usa os seguintes parâmetros:

* Nome da fonte de dados (nvarchar): o nome da fonte de dados externa do tipo RDBMS.
* Consulta (nvarchar): a consulta T-SQL a ser executada em cada fragmento.
* Declaração de parâmetro (nvarchar) - opcional: cadeia de caracteres com definições de tipo de dados para os parâmetros usados no parâmetro Query (como sp_executesql).
* Lista de valores de parâmetro - opcional: lista separada por vírgulas de valores de parâmetro (como sp_executesql).

O sp\_execute\_remote usa a fonte de dados externa fornecida nos parâmetros de invocação para executar a instrução T-SQL especificada nos bancos de dados remotos. Ele usa a credencial da fonte de dados externa para a conexão com o banco de dados do gerenciador de mapa do fragmento e bancos de dados remotos.  

Exemplo:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas

Use as cadeias de conexão SQL Server regulares para conectar seu aplicativo, seu BI e as ferramentas de integração de dados ao banco de dado com suas definições de tabela externa. Certifique-se de que o SQL Server tem suporte como uma fonte de dados para a ferramenta. Em seguida, faça referência ao banco de dados de consulta elástica como qualquer outro banco de dados do SQL Server conectado à ferramenta, e use tabelas externas de sua ferramenta ou aplicativo como se elas fossem tabelas locais.

## <a name="best-practices"></a>Práticas recomendadas

* Verifique se o banco de dados de ponto de extremidade de consulta elástica recebeu acesso ao banco de dados shardmap e a todos os fragmentos por meio dos firewalls do banco de dados SQL.  
* Valide ou imponha a distribuição de dados definida pela tabela externa. Se a distribuição de dados real for diferente da distribuição especificada na definição de tabela, as consultas podem gerar resultados inesperados.
* A consulta elástica atualmente não executa a eliminação de fragmentos quando predicados da chave de fragmentação permitem que ela exclua com segurança determinados fragmentos do processamento.
* A consulta elástica funciona melhor para consultas em que a maior parte da computação pode ser realizada nos fragmentos. Normalmente o melhor desempenho de consulta é obtido com predicados de filtro seletivo que podem ser avaliados nos fragmentos ou junções sobre as chaves de particionamento que podem ser executadas de forma alinhada por partição em todos os fragmentos. Outros padrões de consulta podem precisar carregar grandes quantidades de dados dos fragmentos para o nó de cabeçalho e podem ter um desempenho insatisfatório

## <a name="next-steps"></a>Próximas etapas

* Para obter uma visão geral de consulta elástica, veja [Visão geral de consulta elástica](elastic-query-overview.md).
* Para obter um tutorial sobre particionamento vertical, veja [Introdução à consulta entre bancos de dados (particionamento vertical)](elastic-query-getting-started-vertical.md).
* Para sintaxe e amostras de consultas para dados particionados verticalmente, consulte [Consultando dados particionados verticalmente)](elastic-query-vertical-partitioning.md)
* Para um tutorial sobre particionamento horizontal (fragmentação), consulte [Introdução à consulta elástica para particionamento horizontal (fragmentação)](elastic-query-getting-started.md).
* Consulte [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) para um procedimento armazenado que executa uma instrução Transact-SQL em um único Banco de Dados SQL do Azure remoto ou um conjunto de bancos de dados que serve como fragmentos em um esquema de particionamento horizontal.

<!--Image references-->
[1]: ./media/elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
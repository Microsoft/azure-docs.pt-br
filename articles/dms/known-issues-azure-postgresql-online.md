---
title: Artigo sobre as limitações de migração e os problemas conhecidos das migrações online para o Banco de Dados do Azure para MySQL | Microsoft Docs
description: Saiba mais sobre as limitações de migração e os problemas conhecidos das migrações online para o Banco de Dados do Azure para MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/23/2019
ms.openlocfilehash: 2c8a3f36e04fbedfdd127939d55fab376e3e6b30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "64691944"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Problemas conhecidos/limitações de migração com migrações online para o BD do Azure para PostgreSQL

O problemas e limitações conhecidos associados às migrações online do PostgreSQL para o Banco de Dados do Azure para PostgreSQL são descritos nas seções a seguir. 

## <a name="online-migration-configuration"></a>Configuração de migração online
- A origem do PostgreSQL Server deve estar executando a versão 9.5.11, 9.6.7 ou 10.3 ou posterior. Para obter mais informações, consulte o artigo [Versões com suporte do Banco de Dados do PostgreSQL](../postgresql/concepts-supported-versions.md).
- Há suporte apenas para migrações de mesma versão. Por exemplo, não há suporte para migração do PostgreSQL 9.5.11 para o Banco de Dados do Azure para PostgreSQL 9.6.7.

    > [!NOTE]
    > Para PostgreSQL versão 10, atualmente o DMS dá suporte apenas à versão de migração 10.3 do Banco de Dados do Azure para PostgreSQL. Estamos planejando dar suporte a versões mais recentes do PostgreSQL muito em breve.

- Para habilitar a replicação lógica no arquivo  **postgresql.conf do PostgreSQL de origem** defina os seguintes parâmetros:
    - **wal_level** = lógica
    - **max_replication_slots** = [número máximo de bancos de dados para migração]; se você quiser migrar 4 bancos de dados, defina o valor como 4
    - **max_wal_senders** = [número de bancos de dados executados simultaneamente]; o valor recomendado é 10
- Adicionar o IP do agente DMS à origem do PostgresSQL pg_hba.conf
    1. Anote o endereço IP do DMS após concluir o provisionamento de uma instância do DMS.
    2. Adicione o endereço IP ao arquivo pg_hba.conf conforme mostrado:

        hospedar todos 172.16.136.18/10 md5 host de replicação postgres 172.16.136.18/10 md5

- O usuário deve ter permissão de superusuário no servidor que hospeda o banco de dados de origem
- Além de ter ENUM no esquema do banco de dados de origem, os esquemas do banco de dados de origem e de destino deverão corresponder.
- O esquema no Banco de Dados do Azure para PostgreSQL de destino não deve ter chaves estrangeiras. Use a consulta a seguir para remover as chaves estrangeiras:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Execute a remoção de chave estrangeira (que é a segunda coluna) no resultado da consulta.

- O esquema no Banco de Dados do Azure para PostgreSQL de destino não deve ter gatilhos. Use o seguinte para desabilitar gatilhos no banco de dados de destino:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitações de tipo de dados

- **Limitação**: Se houver um tipo de dados ENUM no banco de dados PostgreSQL de origem, a migração falhará durante a sincronização contínua.

    **Solução alternativa**: Modifique o tipo de dados ENUM para caractere que varie no Banco de Dados do Azure para PostgreSQL.

- **Limitação**: Se não houver nenhuma chave primária nas tabelas, a sincronização contínua falhará.

    **Solução alternativa**: Defina temporariamente uma chave primária para a tabela para migração continuar. Você poderá remover a chave primária após a conclusão da migração de dados.

## <a name="lob-limitations"></a>Limitações de LOB
Colunas de LOB (Objeto Grande) são colunas que podem crescer muito. Para PostgreSQL, exemplos de tipos de dados de LOB incluem XML, JSON, IMAGE, TEXT, etc.

- **Limitação**: Se tipos de dados LOB forem usados como chaves primárias, a migração falhará.

    **Solução alternativa**: Substitua a chave primária por outros tipos de dados ou colunas que não sejam LOB.

- **Limitação**: Se o tamanho da coluna LOB (Objeto Grande) for maior que 32 KB, os dados poderão ser truncados no destino. Você pode verificar o tamanho da coluna de LOB usando esta consulta:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Solução alternativa**: Se você tiver um objeto LOB que é maior do que 32 KB, entre em contato com a equipe de engenharia [fazer migrações de banco de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Limitação**: Se houver colunas LOB na tabela, e não houver conjunto de chaves primárias para a tabela, os dados podem não ser migrados para essa tabela.

    **Solução alternativa**: Defina temporariamente uma chave primária para a tabela para migração continuar. Você poderá remover a chave primária após a conclusão da migração de dados.

## <a name="postgresql10-workaround"></a>Solução alternativa do PostgreSQL10
O PostgreSQL 10.x efetua várias alterações nos nomes das pastas pg_xlog e, portanto, faz com que a migração não seja executada conforme o esperado. Se você estiver migrando do PostgreSQL 10.x para o Banco de Dados do Azure para PostgreSQL 10.3, execute o seguinte script no Banco de Dados do Azure para PostgreSQL de origem para criar uma função wrapper em torno das funções pg_xlog.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="other-limitations"></a>Outras limitações
- O nome do banco de dados não pode incluir um ponto e vírgula (;).
- Não há suporte para cadeia de caracteres de senha que possui chaves de abertura e fechamento {  }. Esta limitação aplica-se tanto à conexão ao PostgreSQL de origem quanto ao Banco de Dados do Azure para PostgreSQL de destino.
- Uma tabela capturada deve ter uma Chave Primária. Se uma tabela não tiver uma chave primária, o resultado das operações de registro EXCLUIR e ATUALIZAR será imprevisível.
- A atualização de um segmento de chave primária é ignorada. Nesses casos, a aplicação de tal atualização será identificada pelo destino como uma atualização que não atualizou nenhuma linha e resultará em um registro gravado na tabela de exceções.
- A migração de várias tabelas com o mesmo nome, mas um caso diferente (por exemplo, tabela1, TABELA1 e Tabela1) pode causar um comportamento imprevisível e, portanto, não tem suporte.
- Há suporte para processamento de alterações [CRIAR | ALTERAR | SOLTAR ] das DDLs da tabela, a menos que sejam mantidas em um bloco de corpo de função/procedimento interno ou em outras construções aninhadas. Por exemplo, a seguinte alteração não será capturada:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- Não há suporte para processamento de alterações (sincronização contínua) das operações TRUNCAR. Não há suporte para migração de tabelas particionadas. Quando uma tabela particionada é detectada, ocorrem as seguintes ações:
    - O banco de dados reportará uma lista de tabelas pai e filho.
    - A tabela será criada no destino como uma tabela regular com as mesmas propriedades das tabelas selecionadas.
    - Se a tabela pai no banco de dados de origem tiver o mesmo valor de Chave Primária que suas tabelas filhas, um erro de "chave duplicada" será gerado.
- No DMS, o limite de bancos de dados a serem migrados em uma única atividade de migração é quatro.
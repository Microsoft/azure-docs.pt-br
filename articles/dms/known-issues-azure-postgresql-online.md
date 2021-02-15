---
title: 'Problemas conhecidos: migrações online do PostgreSQL para o banco de dados do Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas conhecidos e limitações de migração com migrações online do PostgreSQL para o banco de dados do Azure para PostgreSQL usando o serviço de migração de banco de dados do Azure.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: fdefcabdda64402610f115832976ec9f7af81b80
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258822"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Problemas conhecidos/limitações de migração com migrações online do PostgreSQL para o BD do Azure para PostgreSQL

O problemas e limitações conhecidos associados às migrações online do PostgreSQL para o Banco de Dados do Azure para PostgreSQL são descritos nas seções a seguir.

## <a name="online-migration-configuration"></a>Configuração de migração online

- O servidor PostgreSQL de origem deve estar executando a versão 9,4, 9,5, 9,6, 10 ou 11. Para obter mais informações, consulte o artigo [Versões com suporte do Banco de Dados do PostgreSQL](../postgresql/concepts-supported-versions.md).
- Há suporte apenas para migrações para a mesma versão ou para versões posteriores. Por exemplo, há suporte para a migração de PostgreSQL 9,5 para o banco de dados do Azure para PostgreSQL 9,6 ou 10, mas não há suporte para migrar do PostgreSQL 11 para PostgreSQL 9,6.
- Para habilitar a replicação lógica no arquivo **postgresql.conf do PostgreSQL de origem** defina os seguintes parâmetros:
  - **wal_level** = lógico
  - **max_replication_slots** = [pelo menos o número máximo de bancos de dados para migração]; Se você quiser migrar quatro bancos de dados, defina o valor para pelo menos 4.
  - **max_wal_senders** = [número de bancos de dados executados simultaneamente]; o valor recomendado é 10
- Adicionar o IP do agente DMS ao PostgreSQL de origem pg_hba. conf
  1. Anote o endereço IP DMS depois de concluir o provisionamento de uma instância do serviço de migração de banco de dados do Azure.
  2. Adicione o endereço IP ao arquivo pg_hba.conf conforme mostrado:

      ```
          host    all    172.16.136.18/10    md5
          host    replication postgres    172.16.136.18/10     md5
      ```

- O usuário deve ter a função de replicação no servidor que hospeda o banco de dados de origem.
- Os esquemas de banco de dados de origem e de destino devem corresponder.
- O esquema no banco de dados do Azure de destino para o PostgreSQL-Single Server não deve ter chaves estrangeiras. Use a consulta a seguir para remover as chaves estrangeiras:

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

- O esquema no banco de dados do Azure de destino para o PostgreSQL-Single Server não deve ter gatilhos. Use o seguinte para desabilitar gatilhos no banco de dados de destino:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>Limitações de tamanho
- Você pode migrar até 2 TB de dados do PostgreSQL para o BD do Azure para PostgreSQL usando um único serviço DMS.
## <a name="datatype-limitations"></a>Limitações de tipo de dados

  **Limitação**: se não houver nenhuma chave primária em tabelas, as alterações não poderão ser sincronizadas com o banco de dados de destino.

  **Solução alternativa**: defina temporariamente uma chave primária na tabela para que a migração continue. Você poderá remover a chave primária após a conclusão da migração de dados.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Limitações ao migrar online do AWS RDS PostgreSQL

Ao tentar executar uma migração online do AWS RDS PostgreSQL para o banco de dados do Azure para PostgreSQL, você pode encontrar os erros a seguir.

- **Erro**: O valor padrão da coluna '{column}' na tabela '{table}' no banco de dados '{database}' é diferente nos servidores de origem e destino. É '{value on source}' na origem e '{value on target}' no destino.

  **Limitação**: esse erro ocorre quando o valor padrão em um esquema de coluna é diferente entre os bancos de dados de origem e de destino.
  **Solução alternativa**: Verifique se o esquema no destino corresponde ao esquema na origem. Para obter detalhes sobre a migração de esquema, consulte a [documentação de migração online do PostgreSQL do Azure](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Erro**: O banco de dados de destino '{database}' tem '{number of tables}' tabelas, enquanto que o banco de dados de origem '{database}' tem '{number of tables}' tabelas. O número de tabelas nos bancos de dados de origem e destino deve corresponder.

  **Limitação**: esse erro ocorre quando o número de tabelas é diferente entre os bancos de dados de origem e de destino.

  **Solução alternativa**: Verifique se o esquema no destino corresponde ao esquema na origem. Para obter detalhes sobre a migração de esquema, consulte a [documentação de migração online do PostgreSQL do Azure](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Erro:** O banco de dados de origem {Database} está vazio.

  **Limitação**: esse erro ocorre quando o banco de dados de origem está vazio. É muito provável que você tenha selecionado o banco de dados incorreto como fonte.

  **Solução alternativa**: Verifique o banco de dados de origem selecionado para migração e tente novamente.

- **Erro:** O banco de dados de destino {Database} está vazio. Migre o esquema.

  **Limitação**: esse erro ocorre quando não há esquema no banco de dados de destino. Verifique se o esquema no destino corresponde ao esquema na origem.
  **Solução alternativa**: Verifique se o esquema no destino corresponde ao esquema na origem. Para obter detalhes sobre a migração de esquema, consulte a [documentação de migração online do PostgreSQL do Azure](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

## <a name="other-limitations"></a>Outras limitações

- O nome do banco de dados não pode incluir um ponto e vírgula (;).
- Uma tabela capturada deve ter uma Chave Primária. Se uma tabela não tiver uma chave primária, o resultado das operações de registro EXCLUIR e ATUALIZAR será imprevisível.
- A atualização de um segmento de chave primária é ignorada. Nesses casos, a aplicação de tal atualização será identificada pelo destino como uma atualização que não atualizou nenhuma linha e resultará em um registro gravado na tabela de exceções.
- A migração de várias tabelas com o mesmo nome, mas um caso diferente (por exemplo, tabela1, TABELA1 e Tabela1) pode causar um comportamento imprevisível e, portanto, não tem suporte.
- Alterar o processamento de [criar | ALTERAR | SOLTAR | TRUNCATE] não há suporte para a tabela DDLs.
- No serviço de migração de banco de dados do Azure, uma única atividade de migração só pode acomodar até quatro bancos de dados.
- Não há suporte para a migração da tabela pg_largeobject. 

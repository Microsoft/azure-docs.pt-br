---
title: 'Problemas conhecidos: migrações online para o banco de dados do Azure para MySQL'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas conhecidos e limitações de migração com migrações online para o banco de dados do Azure para MySQL ao usar o serviço de migração de banco de dados do Azure.
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
ms.openlocfilehash: 11659bcbdf77d04c0f4e6f8bc7aca30c716fc924
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606882"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problemas de migração online & limitações do Azure DB para MySQL com o serviço de migração de banco de dados do Azure

Os problemas conhecidos e as limitações associados às migrações online do MySQL para o Banco de Dados do Azure para MySQL são descritos nas seções a seguir.

## <a name="online-migration-configuration"></a>Configuração de migração online


- A versão do MySQL Server de origem precisa ser a 5.6.35, a 5.7.18 ou posteriores
- O Banco de Dados do Azure para MySQL dá suporte a:
  - MySQL Community Edition
  - Mecanismo InnoDB
- Migração de mesma versão. Não há suporte para a migração do MySQL 5.6 para o Banco de Dados do Azure para MySQL 5.7. As migrações para o MySQL 8.0 ou dele não são compatíveis.
- Habilite o log binário em my.ini (Windows) ou em my.cnf (Unix)
  - Defina Server_id para qualquer número maior ou igual a 1, por exemplo, Server_id=1 (somente para o MySQL 5.6)
  - Defina log-bin = \<path> (somente para o MySQL 5.6)
  - Defina binlog_format = row
  - Expire_logs_days = 5 (recomendado, apenas para o MySQL 5.6)
- O usuário precisa ter a função ReplicationAdmin.
- As ordenações definidas para o banco de dados MySQL de origem devem ser as mesmas que as definidos no Banco de Dados do Azure para MySQL de destino.
- O esquema precisa corresponder entre o banco de dados MySQL de origem e o banco de dados de destino no Banco de Dados do Azure para MySQL.
- O esquema no Banco de Dados do Azure para MySQL de destino não pode ter chaves estrangeiras. Use a consulta a seguir para remover as chaves estrangeiras:
    ```sql
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
      CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
        KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
        AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Execute a remoção de chave estrangeira (que é a segunda coluna) no resultado da consulta.
- O esquema no Banco de Dados do Azure para MySQL de destino não pode ter nenhum gatilho. Para remover os gatilhos no banco de dados de destino:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitações de tipo de dados

- **Limitação**: se houver um tipo de dados JSON no banco de dados MySQL de origem, a migração falhará durante a sincronização contínua.

    **Solução alternativa**: modifique o tipo de dados JSON para texto médio ou longtext no banco de dados MySQL de origem.

- **Limitação**: se não houver nenhuma chave primária nas tabelas, a sincronização contínua falhará.

    **Solução alternativa**: defina temporariamente uma chave primária na tabela para que a migração continue. Você poderá remover a chave primária após a conclusão da migração de dados.

## <a name="lob-limitations"></a>Limitações de LOB

As colunas de LOB (Objeto Grande) são aquelas cujo tamanho pode aumentar muito. Para MySQL, texto médio, longtext, BLOB, mediumblob, longblob, etc., são alguns dos tipos de texto de LOB.

- **Limitação**: se tipos de dados de LOB forem usados como chaves primárias, a migração falhará.

    **Solução alternativa**: substitua chave primária por outros tipos de dados ou colunas que não sejam de LOB.

- **Limitação**: se o comprimento da coluna de objeto grande (LOB) for maior do que o parâmetro "limitar tamanho de LOB" (não deve ser maior que 64 KB), os dados poderão ser truncados no destino. Você pode verificar o tamanho da coluna de LOB usando esta consulta:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Solução alternativa**: se você tiver um objeto LOB maior que 64 KB, use o parâmetro "permitir tamanho de LOB ilimitado". Observe que as migrações que usam o parâmetro "permitir tamanho de LOB ilimitado" serão mais lentas do que as migrações usando o parâmetro "limitar tamanho de LOB".

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Limitações ao migrar online do AWS RDS MySQL

Ao tentar executar uma migração online do AWS RDS MySQL para o banco de dados do Azure para MySQL, você pode vir os erros a seguir.

- **Erro:** O banco {0} de dados ' ' tem chave (s) estrangeira (ões) no destino. Corrija o destino e inicie uma nova atividade de migração de dados. Execute o script abaixo no destino para listar as chaves estrangeiras

  **Limitação**: se você tiver chaves estrangeiras em seu esquema, a carga inicial e a sincronização contínua da migração falharão.
  **Solução alternativa**: execute o seguinte script no MySQL Workbench para extrair o script de remoção de chave estrangeira e adicionar script de chave estrangeira:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Erro:** O banco {0} de dados ' ' não existe no servidor. O servidor de origem do MySQL fornecido diferencia maiúsculas de minúsculas. Verifique o nome do banco de dados.

  **Limitação**: ao migrar um banco de dados MySQL para o Azure usando a CLI (interface de linha de comando), os usuários poderão atingir esse erro. O serviço não pôde localizar o banco de dados no servidor de origem, o que pode ser porque você pode ter fornecido um nome de banco de dados incorreto ou o banco de dados não existe no servidor listado. Observação os nomes de banco de dados diferenciam maiúsculas de minúsculas.

  **Solução alternativa**: forneça o nome exato do banco de dados e tente novamente.

- **Erro:** Há tabelas com o mesmo nome no banco de dados ' {database} '. O Banco de Dados do Azure para MySQL não oferece suporte a diferenciação de maiúsculas de minúsculas para tabelas.

  **Limitação**: esse erro ocorre quando você tem duas tabelas com o mesmo nome no banco de dados de origem. O banco de dados do Azure para MySQL não dá suporte a tabelas com diferenciação de maiúsculas

  **Solução alternativa**: atualize os nomes de tabela para serem exclusivos e tente novamente.

- **Erro:** O banco de dados de destino {Database} está vazio. Migre o esquema.

  **Limitação**: esse erro ocorre quando o banco de dados de destino do banco de dados do Azure para MySQL não tem o esquema necessário. A migração de esquema é necessária para habilitar a migração de dados para o destino.

  **Solução alternativa**: [migre o esquema](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema) do banco de dados de origem para o banco de dados de destino.

## <a name="other-limitations"></a>Outras limitações

- Não há suporte para cadeia de caracteres de senha com colchetes {} de abertura e fechamento em seu início e final. Essa limitação aplica-se tanto à conexão com o MySQL de origem quanto com o Banco de Dados do Azure para MySQL de destino.
- Não há suporte para as DDLs a seguir:
  - Todas as DDLs de partição
  - Remover tabela
  - Renomear tabela
- Não há suporte para usar a instrução *alter table <table_name> add column <column_name>* para adicionar colunas ao início ou ao meio de uma tabela. A instrução *alter table <table_name> add column <column_name>* adiciona a coluna ao final da tabela.
- Não há suporte para índices criados com apenas uma parte dos dados da coluna. A instrução a seguir é um exemplo que cria um índice usando apenas uma parte dos dados da coluna:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- No Azure Database Migration Service, o limite de bancos de dados a serem migrados em uma única atividade de migração é de quatro.

- O DMS do Azure não dá suporte à ação referencial CASCADE, que ajuda a excluir ou atualizar automaticamente uma linha correspondente na tabela filho quando uma linha é excluída ou atualizada na tabela pai. Para obter mais informações, na documentação do MySQL, confira a seção Ações Referenciais do artigo [Restrições de FOREIGN KEY](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html). O DMS do Azure exige que você remova as restrições de chave estrangeira do servidor de banco de dados de destino durante o carregamento de dados inicial, e você não pode usar as ações referenciais. Se a sua carga de trabalho depende da atualização de uma tabela filho relacionada por meio dessa ação referencial, recomendamos que você execute [despejo e restauração](../mysql/concepts-migrate-dump-restore.md). 

- **Erro:** Tamanho de linha muito grande (> 8126). A alteração de algumas colunas para texto ou BLOB pode ajudar. No formato de linha atual, o prefixo de BLOB de 0 bytes é armazenado embutido.

  **Limitação**: esse erro ocorre quando você está migrando para o banco de dados do Azure para MySQL usando o mecanismo de armazenamento InnoDB e qualquer tamanho de linha de tabela é muito grande (>8126 bytes).

  **Solução alternativa**: Atualize o esquema da tabela que tem um tamanho de linha maior que 8126 bytes. Não é recomendável alterar o modo estrito porque os dados serão truncados. Não há suporte para a alteração do page_size.
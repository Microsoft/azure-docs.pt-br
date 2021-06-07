---
title: Migrar um banco de dados – Banco de Dados do Azure para PostgreSQL – servidor individual
description: Descreve como extrair um banco de dados PostgreSQL para um arquivo de script e importar os dados para o banco de dados de destino desse arquivo.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d0f1824a1e4754dddea26484c836df79e95246d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580973"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrar seu banco de dados PostgreSQL usando exportar e importar
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Use [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) para extrair um banco de dados PostgreSQL para um arquivo de script, e [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para importar os dados para o banco de dados de destino desse arquivo.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [Servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall a fim de permitir o acesso e um banco de dados sob ele.
- O utilitário de linha de comando [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) instalado
- O utilitário de linha de comando [psql](https://www.postgresql.org/docs/current/static/app-psql.html) instalado

Execute estas etapas para exportar e importar o banco de dados PostgreSQL.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Criar um arquivo de script usando pg_dump que contém os dados a serem carregados
Para exportar seu banco de dados PostgreSQL existente localmente ou em uma VM para um arquivo de script sql, execute o seguinte comando em seu ambiente existente:

```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Por exemplo, se você tiver um servidor local e um banco de dados chamado **testdb** nele:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importar os dados no Banco de Dados do Azure para PostgreSQL de destino
Você pode usar a linha de comando psql e o parâmetro --dbname (-d) para importar os dados para o servidor do Banco de Dados do Azure para PostgreSQL e carregar o arquivo sql.

```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user> --dbname=<target database name>
```
Este exemplo usa um utilitário psql e um arquivo de script nomeado **testdb.sql** da etapa anterior para importar dados para o banco de dados **mypgsqldb** no servidor de destino **mydemoserver.postgres.database.azure.com**.

Para um **Servidor individual**, use este comando 
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

Para **Servidor flexível**, use este comando  
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin --dbname=mypgsqldb
```



## <a name="next-steps"></a>Próximas etapas
- Para migrar um banco de dados PostgreSQL usando despejar e restaurar, veja [Migrar seu banco de dados PostgreSQL usando despejar e restaurar](howto-migrate-using-dump-and-restore.md).
- Para obter mais informações de como migrar bancos de dados para o Banco de Dados do Azure para PostgreSQL, confira o [Guia de Migração de Banco de Dados](https://aka.ms/datamigration).

---
title: Despejar e restaurar-banco de dados do Azure para PostgreSQL-servidor único
description: Descreve como extrair um banco de dados PostgreSQL em um arquivo de despejo e restaurá-lo a partir de um arquivo criado por pg_dump no banco de dados do Azure para PostgreSQL-servidor único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4fe15d1bd23f36b7289c54bedf575ae4760600e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710797"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Migrar seu banco de dados PostgreSQL usando despejar e restaurar
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

Use [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) para extrair um banco de dados PostgreSQL para um arquivo de despejo, e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) para restaurar o banco de dados PostgreSQL de um arquivo criado por pg_dump.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [Servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md) com regras de firewall a fim de permitir o acesso e um banco de dados sob ele.
- Utilitários de linha de comando [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) instalados

Siga as etapas abaixo para despejar e restaurar seu banco de dados PostgreSQL:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Criar um arquivo de despejo usando pg_dump que contém os dados a serem carregados
Para fazer backup de um banco de dados PostgreSQL que existe localmente ou em uma VM, execute o seguinte comando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Por exemplo, se você tiver um servidor local e um banco de dados chamado **testdb** nele
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Restaurar os dados para o banco de dado de destino do Azure para PostgreSQL usando pg_restore
Depois de criar o banco de dados de destino, você poderá usar o comando pg_restore e o parâmetro -d, --dbname para restaurar os dados no banco de dados de destino do arquivo de despejo.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

A inclusão do parâmetro --no-owner faz com que todos os objetos criados durante a restauração sejam de propriedade do usuário especificado com --username. Para obter mais informações, consulte a documentação oficial do PostgreSQL em [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Se o seu servidor PostgreSQL exigir conexões TLS/SSL (ativado por padrão no banco de dados do Azure para servidores PostgreSQL), defina uma variável de ambiente `PGSSLMODE=require` para que a ferramenta de pg_restore se conecte ao TLS. Sem o TLS, o erro pode ser lido  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Na linha de comando do Windows, execute o comando `SET PGSSLMODE=require` antes de executar o comando pg_restore. No Linux ou o Bash, execute o comando `export PGSSLMODE=require` antes de executar o comando pg_restore.
>

Neste exemplo, restauramos os dados no arquivo de despejo **testdb.dump** no banco de dados **mypgsqldb** no servidor de destino **mydemoserver.postgres.database.azure.com**.

Aqui está um exemplo de como usar essa **pg_restore** para **um único servidor**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```
Veja um exemplo de como usar essa **pg_restore** para o **servidor flexível**:

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```
---

## <a name="optimizing-the-migration-process"></a>Otimizando o processo de migração

Uma maneira de migrar seu banco de dados PostgreSQL existente para o serviço Banco de Dados do Azure para PostgreSQL é fazer o backup do banco de dados na origem e restaurá-lo no Azure. Para minimizar o tempo necessário para concluir a migração, considere usar os seguintes parâmetros com os comandos de backup e restauração.

> [!NOTE]
> Para obter informações de sintaxe detalhadas, confira os artigos [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Para o backup
- Faça o backup com a opção -Fc para que você possa executar a restauração em paralelo para acelerá-la. Por exemplo:

    ```bash
    pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
    ```

### <a name="for-the-restore"></a>Para a restauração
- Sugerimos que você mova o arquivo de backup para uma VM do Azure na mesma região que o servidor do Banco de Dados do Azure para PostgreSQL de destino da migração e execute o pg_restore dessa VM para reduzir a latência de rede. Também é recomendado que a VM seja criada com a [rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) habilitada.

- Isso já deve estar feito por padrão, mas abra o arquivo de despejo para verificar se as instruções create index estão após a inserção dos dados. Se não estiverem, coloque as instruções create index após a inserção dos dados.

- Restaure com switches-FC e-j *#* para paralelizar a restauração. *#* é o número de núcleos no servidor de destino. Você também pode tentar *#* definir como duas vezes o número de núcleos do servidor de destino para ver o impacto. Por exemplo:

Aqui está um exemplo de como usar essa **pg_restore** para **um único servidor**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
```
Veja um exemplo de como usar essa **pg_restore** para o **servidor flexível**:
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
 ```

- Você também pode editar o arquivo de despejo, adicionando o comando *set synchronous_commit = off;* ao início e o comando *set synchronous_commit = on;* ao final. Se ele não for ativado no final, antes que os aplicativos alterem os dados, poderá ocorrer uma perda subsequente de dados.

- No servidor de destino do Banco de Dados do Azure para PostgreSQL, considere o seguinte antes da restauração:
    - Desabilite o acompanhamento de desempenho de consulta, já que essas estatísticas não são necessárias durante a migração. Você pode fazer isso definindo pg_stat_statements.track, pg_qs.query_capture_mode e pgms_wait_sampling.query_capture_mode como NONE.

    - Use uma SKU de alta computação e memória alta, como 32 vCore Otimizado para Memória, para acelerar a migração. Você pode facilmente dimensionar de volta para sua SKU preferencial depois de concluir a restauração. Quanto maior a SKU, maior o paralelismo alcançado aumentando o parâmetro `-j` correspondente no comando pg_restore.

    - Mais IOPS no servidor de destino pode melhorar o desempenho da restauração. Você pode provisionar mais IOPS aumentando o tamanho de armazenamento do servidor. Essa configuração não é reversível, mas pense se um IOPS mais alto pode beneficiar sua carga de trabalho real no futuro.

Lembre-se de testar e validar esses comandos em um ambiente de teste antes de usá-los em produção.

## <a name="next-steps"></a>Próximas etapas
- Para migrar um banco de dados PostgreSQL usando exportar e importar, veja [Migrar seu banco de dados PostgreSQL usando exportar e importar](howto-migrate-using-export-and-import.md).
- Para obter mais informações de como migrar bancos de dados para o Banco de Dados do Azure para PostgreSQL, confira o [Guia de Migração de Banco de Dados](https://aka.ms/datamigration).

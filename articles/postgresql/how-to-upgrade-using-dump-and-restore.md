---
title: Atualizar usando despejo e restauração-banco de dados do Azure para PostgreSQL-servidor único
description: Descreve os métodos de atualização offline usando os bancos de dados de despejo e restauração para migrar para uma versão mais recente do Azure database for PostgreSQL-Single Server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 42bbe1c9f4056ae0dae0ccd59b452db90a7c63c5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493654"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Atualizar seu banco de dados PostgreSQL usando despejo e restauração

Você pode atualizar o servidor PostgreSQL implantado no banco de dados do Azure para PostgreSQL-servidor único migrando seus bancos de dados para um servidor de versão principal mais alto usando os métodos a seguir.
* Método **offline** usando PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) que incorre em tempo de inatividade para migrar os dados. Este documento aborda esse método de atualização/migração.
* Método **online** usando o [serviço de migração de banco de dados](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS). Esse método fornece uma migração de tempo de inatividade reduzida e mantém o banco de dados de destino em sincronia com a origem e você pode escolher quando recortar. No entanto, há poucos pré-requisitos e restrições a serem resolvidos para o uso do DMS. Para obter detalhes, consulte a [documentação do DMS](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md). 

 A tabela a seguir fornece algumas recomendações com base em tamanhos e cenários de banco de dados.

| **Banco de dados/cenário** | **Despejo/restauração (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Você tem um banco de dados pequeno e pode pagar tempo de inatividade para atualizar  | X | |
| Bancos de dados pequenos (< 10 GB)  | X | X | 
| Bancos de pequenos-médios (10 GB – 100 GB) | X | X |
| Bancos de dados grandes (> 100 GB) |  | X |
| Pode arcar com o tempo de inatividade para atualizar (independentemente do tamanho do banco de dados) | X |  |
| Pode resolver os [pré-requisitos](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites)do DMS, incluindo uma reinicialização? |  | X |
| Pode evitar tabelas DDLs e não registradas durante o processo de atualização? | |  X |

Este guia fornece algumas metodologias de migração offline e exemplos para mostrar como você pode migrar do servidor de origem para o servidor de destino que executa uma versão mais recente do PostgreSQL.

> [!NOTE]
> O despejo e a restauração do PostgreSQL podem ser executados de várias maneiras. Você pode optar por migrar usando um dos métodos fornecidos neste guia ou escolher quaisquer formas alternativas para atender às suas necessidades. Para obter uma sintaxe detalhada de despejo e restauração com parâmetros adicionais, consulte os artigos [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Pré-requisitos para usar despejo e restauração com o banco de dados do Azure para PostgreSQL
 
Para percorrer este guia de instruções, você precisa de:

- Um banco de dados PostgreSQL de **origem** executando 9,5, 9,6 ou 10 que você deseja atualizar
- Um servidor de banco de dados PostgreSQL de **destino** com a versão principal desejada do [servidor do banco de dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md). 
- Um sistema cliente PostgreSQL para executar os comandos dump e Restore.
  - Pode ser um cliente Linux ou Windows com PostgreSQL instalado e tem [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) utilitários de linha de comando instalados. 
  - Como alternativa, você pode usar [Azure cloud Shell](https://shell.azure.com) ou clicando na Azure cloud Shell na barra de menus na parte superior direita da [portal do Azure](https://portal.azure.com). Você precisará fazer logon em sua conta `az login` antes de executar os comandos dump e Restore.
- O cliente PostgreSQL é, de preferência, executado na mesma região que os servidores de origem e de destino. 


## <a name="additional-details-and-considerations"></a>Detalhes e considerações adicionais
- Você pode encontrar a cadeia de conexão para os bancos de dados de origem e de destino clicando em "cadeias de conexão" no Portal. 
- Você pode estar executando mais de um banco de dados em seu servidor. Você pode encontrar a lista de bancos de dados conectando-se ao seu servidor de origem e executando o `\l` .
- Crie bancos de dados correspondentes no servidor de banco de dados de destino.
- Você pode ignorar a atualização `azure_maintenance` ou os bancos de dados de modelo.
- Consulte as tabelas acima para determinar se o banco de dados é adequado para esse modo de migração.
- Se você quiser usar Azure Cloud Shell, observe que a sessão atinge o tempo limite após 20 minutos. Se o tamanho do seu banco de dados for < 10 GB, você poderá concluir a atualização sem que o tempo limite da sessão seja atingido. Caso contrário, talvez você precise manter a sessão aberta por outros meios, como pressionar a <Enter> tecla uma vez em 10-15 minutos. 


## <a name="example-database-used-in-this-guide"></a>Banco de dados de exemplo usado neste guia

Neste guia, os seguintes servidores de origem e de destino e nomes de banco de dados são usados para ilustrar com exemplos.

 | **Descrição** | **Valor** |
 | ------- | ------- |
 | Servidor de origem (v 9.5) | pg-95.postgres.database.azure.com |
 | Banco de dados de origem | bench5gb |
 | Tamanho do banco de dados de origem | 5 GB |
 | Nome de usuário de origem | pg@pg-95 |
 | Servidor de destino (v11) | pg-11.postgres.database.azure.com |
 | Banco de dados de destino | bench5gb |
 | Nome de usuário de destino | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Atualizar seus bancos de dados usando métodos de migração offline
Você pode optar por usar um dos métodos descritos nesta seção para suas atualizações. Você pode usar as dicas a seguir ao executar as tarefas.

- Se você estiver usando a mesma senha para a origem e o banco de dados de destino, poderá definir a `PGPASSWORD=yourPassword` variável de ambiente.  Em seguida, você não precisa fornecer a senha toda vez que executar comandos como psql, pg_dump e pg_restore.  Da mesma forma, você pode configurar variáveis adicionais como `PGUSER` , `PGSSLMODE` etc., consulte [variáveis de ambiente do PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Se o seu servidor PostgreSQL exigir conexões TLS/SSL (ativado por padrão no banco de dados do Azure para servidores PostgreSQL), defina uma variável de ambiente `PGSSLMODE=require` para que a ferramenta de pg_restore se conecte ao TLS. Sem o TLS, o erro pode ser lido  `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- Na linha de comando do Windows, execute o comando `SET PGSSLMODE=require` antes de executar o comando pg_restore. No Linux ou o Bash, execute o comando `export PGSSLMODE=require` antes de executar o comando pg_restore.

### <a name="method-1-migrate-using-dump-file"></a>Método 1: migrar usando o arquivo de despejo

Esse método envolve duas etapas. Primeiro, é criar um despejo do servidor de origem. A segunda etapa é restaurar o arquivo de despejo para o servidor de destino. Para obter mais detalhes, consulte a documentação [migrar usando despejo e restauração](howto-migrate-using-dump-and-restore.md) . Esse é o método recomendado se você tiver grandes bancos de dados e o sistema cliente tiver armazenamento suficiente para armazenar o arquivo de despejo.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>Método 2: migrar usando o streaming dos dados de despejo para o banco de dado de destino

Se você não tiver um cliente PostgreSQL ou quiser usar Azure Cloud Shell, poderá usar esse método. O despejo de banco de dados é transmitido diretamente para o servidor de banco de dados de destino e não armazena o despejo no cliente. Portanto, isso pode ser usado com um cliente com armazenamento limitado e até mesmo pode ser executado a partir do Azure Cloud Shell. 

1. Verifique se o banco de dados existe no servidor de destino usando o `\l` comando. Se o banco de dados não existir, crie o banco de dados.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Execute o despejo e a restauração como uma única linha de comando usando um pipe. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Por exemplo,

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Depois que o processo de atualização (migração) for concluído, você poderá testar seu aplicativo com o servidor de destino. 
4. Repita esse processo para todos os bancos de dados no servidor.

 Por exemplo, a tabela a seguir ilustra o tempo necessário para migrar usando o método de despejo de streaming. Os dados de exemplo são populados usando [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Como seu banco de dados pode ter diferentes números de objetos com tamanhos variados do que pgbench tabelas e índices gerados, é altamente recomendável testar o despejo e a restauração do banco de dados para entender o tempo real necessário para atualizar seu banco de dados. 

| **Tamanho do banco de dados** | **Tempo gasto** | 
| ----- | ------ |
| 1 GB  | 1-2 minutos |
| 5 GB | 8-10 minutos |
| 10 GB | 15-20 minutos |
| 50 GB | 1 a 1,5 horas |
| 100 GB | 2,5 a 3 horas|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>Método 3: migrar usando o despejo paralelo e a restauração 

Você pode considerar esse método se tiver poucas tabelas maiores em seu banco de dados e quiser paralelizar o processo de despejo e restauração para esse banco de dados. Você também precisa de armazenamento suficiente no seu sistema cliente para acomodar os despejos de backup. Esse processo paralelo de despejo e restauração reduz o tempo de consumo para concluir a migração inteira. Por exemplo, o banco de dados de 50 GB pgbench que levou de 1 a 1,5 h para migrar foi concluído usando o método 1 e 2 levou menos de 30 minutos usando esse método.

1. Para cada banco de dados no servidor de origem, crie um banco de dados correspondente no servidor de destino.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Por exemplo,
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Execute o comando pg_dump em um formato de diretório com número de trabalhos = 4 (número de tabelas no banco de dados). Com uma camada de computação maior e com mais tabelas, você pode aumentá-la para um número mais alto. Esse pg_dump criará um diretório para armazenar arquivos compactados para cada trabalho.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Por exemplo,
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Em seguida, restaure o backup no servidor de destino.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Por exemplo,
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> O processo mencionado neste documento também pode ser usado para atualizar seu banco de dados do Azure para PostgreSQL-servidor flexível, que está em versão prévia. A principal diferença é que a cadeia de conexão para o destino do servidor flexível é sem o `@dbName` .  Por exemplo, se o nome de usuário for `pg` , o nome de usuários do servidor único na cadeia de conexão será `pg@pg-95` , enquanto com o servidor flexível, você pode simplesmente usar `pg` .

## <a name="next-steps"></a>Próximas etapas

- Depois de estar satisfeito com a função de banco de dados de destino, você pode remover o servidor de banco de dados antigo. 
- Se você quiser usar o mesmo ponto de extremidade de banco de dados que o servidor de origem, depois de ter excluído o servidor de banco de dados de origem antigo, você poderá criar uma réplica de leitura com o nome do servidor de banco de dados antigo. Quando o estado Steady for estabelecido, você poderá interromper a réplica, o que promoverá o servidor de réplica para ser um servidor independente. Consulte [Replicação](./concepts-read-replicas.md) para obter mais detalhes.
- Lembre-se de testar e validar esses comandos em um ambiente de teste antes de usá-los em produção.
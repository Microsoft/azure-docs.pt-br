---
title: 'Tutorial: Usar o Serviço de Migração de Banco de Dados do Azure para realizar uma migração online do PostgreSQL para o Banco de Dados do Azure para PostgreSQL | Microsoft Docs'
description: Saiba como fazer a migração online do PostgreSQL local para o Banco de Dados do Azure para PostgreSQL usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: 96bfb80602efe8e63f814fc9bf6cff3ae52e5983
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461528"
---
# <a name="tutorial-migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Tutorial: Migrar o PostgreSQL para o Banco de Dados do Azure para PostgreSQL online usando o DMS

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância local do PostgreSQL para o [Banco de Dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/) com um tempo de inatividade mínimo. Em outras palavras, a migração pode ser obtida com o mínimo de tempo de inatividade para o aplicativo. Neste tutorial, você vai migrar o banco de dados de exemplo **DVD Rental** de uma instância local do PostgreSQL 9.6 para o Banco de Dados do Azure para PostgreSQL usando uma atividade de migração online no Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Migrar o esquema de exemplo usando o utilitário pg_dump.
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Baixe e instale o [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 ou 10. A versão do PostgreSQL Server de origem deve ser 9.5.11, 9.6.7, 10 ou posterior. Para obter mais informações, consulte o artigo [Versões com suporte do Banco de Dados do PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Além disso, a versão do PostgreSQL local deve corresponder à versão do Banco de Dados do Azure para PostgreSQL. Por exemplo, o PostgreSQL 9.5.11.5 pode migrar apenas o Banco de Dados do Azure para PostgreSQL 9.5.11, não para a versão 9.6.7.

    > [!NOTE]
    > Para PostgreSQL versão 10, atualmente o DMS dá suporte apenas à versão de migração 10.3 do Banco de Dados do Azure para PostgreSQL.

* [Criar uma instância no Banco de Dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).  
* Criar uma VNET (Rede Virtual) do Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma VNET, confira a [Documentação da Rede Virtual](https://docs.microsoft.com/azure/virtual-network/) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da VNET, se você usar ExpressRoute com emparelhamento de rede para Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a Internet.

* Verifique se as regras do Grupo de Segurança de Rede (NSG) de VNET não bloqueiam as seguintes portas de comunicação de entrada com o Serviço de Migração de Banco de Dados do Azure: 443, 53, 9354, 445, 12000. Veja mais detalhes sobre a filtragem de tráfego do NSG da VNet do Azure no artigo [Filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o servidor PostgreSQL de origem, que por padrão é a porta TCP 5432.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
* Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível de servidor para o Banco de Dados do Azure para PostgreSQL a fim de permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-rede da VNet usado para o Serviço de Migração de Banco de Dados do Azure.
* Há dois métodos para invocar a CLI:

  * No canto superior direito do portal do Azure, marque o botão Cloud Shell:

       ![Botão Cloud Shell no portal do Azure](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Instale e execute a CLI localmente. A CLI 2.0 é a ferramenta de linha de comando para gerenciar recursos do Azure.

       Para baixar a CLI, siga as instruções no artigo [Instalar a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). O artigo também lista as plataformas que dão suporte à CLI 2.0.

       Para configurar o WSL (Subsistema do Windows para Linux), siga as instruções no [Guia de instalação do Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)

* Habilite a replicação lógica no arquivo postgresql.config e defina os seguintes parâmetros:

  * wal_level = **lógico**
  * max_replication_slots = [número de slots]; é recomendável definir como **5 slots**
  * max_wal_senders =[número de tarefas simultâneas] – O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas; é recomendável definir como **10 tarefas**

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de banco de dados, como procedimentos armazenados, índices e esquemas de tabela, é necessário extrair o esquema do banco de dados de origem e aplicar ao banco de dados.

1. Use o comando pg_dump -s para criar um arquivo de despejo de esquema para um banco de dados. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para despejar o arquivo de esquema do banco de dados dvdrental:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Para obter mais informações sobre como usar o utilitário pg_dump, confira os exemplos do tutorial [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Crie um banco de dados vazio no ambiente de destino, que é o Banco de Dados do Azure para PostgreSQL.

    Confira o artigo [Criar um Banco de Dados do Azure para PostgreSQL Server no portal do Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) para obter detalhes sobre como criar um banco de dados e se conectar a ele.

3. Importe o esquema para o banco de dados de destino criado restaurando o arquivo de despejo do esquema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Se você tiver chaves estrangeiras em seu esquema, o carregamento inicial e a sincronização contínua da migração falharão. Execute o script a seguir em PgAdmin ou em psql para extrair o script de chave estrangeira de descarte e adicione o script de chave estrangeira ao destino (Banco de Dados do Azure para PostgreSQL).
  
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

5. Os gatilhos dos dados (gatilho de inserção ou atualização) aplicarão a integridade de dados no destino à frente dos dados replicados da origem. É recomendável desabilitar os gatilhos em todas as tabelas **no destino** durante a migração e reabilitar os gatilhos após sua conclusão.

    Para desabilitar gatilhos no banco de dados de destino, use o seguinte comando:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Se houver tipo de dados ENUM em alguma tabela, é recomendável que você a atualize temporariamente para um tipo de dados 'character varying' na tabela de destino. Depois que a replicação de dados for feita, reverta o tipo de dados para ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Como provisionar uma instância do DMS usando a CLI

1. Instale a extensão de sincronização do DMS:
   * Execute o seguinte comando para entrar no Azure:
       ```
       az login
       ```

   * Quando solicitado, abra um navegador da Web e insira um código para autenticar o dispositivo. Siga as instruções conforme listadas.
   * Adicione a extensão dms:
       * Para listar as extensões disponíveis, execute o seguinte comando:

           ```
           az extension list-available –otable
           ```

       * Para instalar a extensão, execute o seguinte comando:

           ```
           az extension add –n dms-preview
           ```

   * Para verificar se você tem a extensão dms instalada corretamente, execute o seguinte comando:

       ```
       az extension list -otable
       ```
       Você deve ver o seguinte resultado:

       ```
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

   * É possível exibir todos os comandos com suporte no DMS a qualquer momento executando:

       ```
       az dms -h
       ```

   * Se você tiver várias assinaturas do Azure, execute o comando a seguir para definir a assinatura que deseja usar para provisionar uma instância do serviço DMS.

        ```
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Provisione uma instância do DMS executando o seguinte comando:

   ```
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name BusinessCritical_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Por exemplo, o seguinte comando criará um serviço em:

   * Localização: Leste dos EUA 2
   * Assinatura: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Nome do Grupo de Recursos: PostgresDemo
   * Nome do Serviço de DMS: PostgresCLI

   ```
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name BusinessCritical_4vCores
   ```

   A criação da instância do serviço DMS leva cerca de 10 a 12 minutos.

3. Para identificar o endereço IP do agente do DMS para e poder adicioná-lo ao arquivo Postgres pg_hba, execute o seguinte comando:

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Por exemplo:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Você verá um resultado semelhante ao seguinte endereço: 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. Adicione o endereço IP do agente do DMS ao arquivo Postgres pg_hba.

    * Anote o endereço IP do DMS depois de concluir o provisionamento no DMS.
    * Adicione o endereço IP ao arquivo pg_hba na origem, semelhante à seguinte entrada:

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. Em seguida, crie um projeto de migração do PostgreSQL executando o seguinte comando:
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Por exemplo, o comando abaixo cria um projeto usando estes parâmetros:

   * Localização: Centro-Oeste dos EUA
   * Nome do Grupo de Recursos: PostgresDemo
   * Nome do Serviço: PostgresCLI
   * Nome do projeto: PGMigration
   * Plataforma de origem: PostgreSQL
   * Plataforma de destino: AzureDbForPostgreSql

     ```
     az dms project create -l eastus2 -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Crie uma tarefa de migração do PostgreSQL usando as etapas a seguir.

    Esta etapa inclui o uso do IP de origem, da ID de usuário e da senha, do IP de destino, da ID de usuário, da senha e do tipo de tarefa para estabelecer a conectividade.

   * Para ver uma lista completa de opções, execute o comando:

       ```
       az dms project task create -h
       ```

       No caso das conexões de origem e de destino, o parâmetro de entrada se refere a um arquivo JSON que contém a lista de objetos.

       O formato do objeto JSON de conexão para conexões do PostgreSQL.
        
       ```
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * Há também um arquivo JSON de opção de banco de dados que lista os objetos JSON. Para o PostgreSQL, o formato do objeto JSON de opções do banco de dados é mostrado abaixo:

       ```
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Crie um arquivo JSON com o Bloco de Notas, copie os comandos a seguir, cole-os no arquivo e salve o arquivo em C:\DMS\source.json.

        ```
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Crie outro arquivo denominado target.json e salve como C:\DMS\target.json. Inclua os seguintes comandos:

       ```
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Crie um arquivo JSON de opções do banco de dados que lista o inventário como o banco de dados a ser migrado:

       ``` 
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Execute o comando a seguir, que usa a origem, o destino e os arquivos JSON de opções do banco de dados.

       ``` 
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     Neste ponto, você enviou com sucesso uma tarefa de migração.

7. Para mostrar o progresso da tarefa, execute o seguinte comando:

   ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   OU

    ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. Você também pode consultar o migrationState na saída da expansão:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>Noções básicas do status da tarefa de migração

Há vários parâmetros que indicam o progresso da migração no arquivo de saída. Por exemplo, confira o arquivo de saída abaixo:

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>Tarefa de migração de substituição

O banco de dados estará pronto para substituição quando o carregamento completo terminar. Dependendo do estado de ocupação do servidor de origem com as novas transações, a tarefa do DMS poderá continuar a aplicar as alterações após a conclusão do carregamento completo.

Para verificar se todos os dados estão atualizados, valide as contagens de linhas entre os bancos de dados de origem e de destino. Por exemplo, você pode usar o seguinte comando:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1. Execute a tarefa de migração de substituição do banco de dados usando o seguinte comando:

    ```
    az dms project task cutover -h
    ```

    Por exemplo:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --database-name Inventory
    ```

2. Para monitorar o progresso da substituição, execute o seguinte comando:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>Limpeza de tarefa, projeto, serviço

Se você precisar cancelar ou excluir uma tarefa, um projeto ou um serviço DMS, execute o cancelamento na sequência a seguir:

* Cancelar as tarefas em execução
* Excluir a tarefa
* Excluir o projeto
* Excluir o serviço DMS

1. Para cancelar uma tarefa em execução, use o seguinte comando:

    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Para excluir uma tarefa em execução, use o seguinte comando:
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Para cancelar um projeto em execução, use o seguinte comando:
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Para excluir um projeto em execução, use o seguinte comando:
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Para excluir o serviço DMS, use o seguinte comando:

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados do Azure para PostgreSQL, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre o Banco de Dados do Azure para PostgreSQL, consulte o artigo [O que é o Banco de Dados do Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
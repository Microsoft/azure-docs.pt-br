---
title: 'Tutorial: Usar o Serviço de Migração de Banco de Dados do Azure para uma migração online do RDS MySQL para o Banco de Dados do Azure para MySQL | Microsoft Docs'
description: Saiba como realizar uma migração online do RDS MySQL para o Banco de Dados do Azure para MySQL usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 09b90f4b53750b94c0ecee7290d6b5405c984ff9
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154876"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Migrar o RDS MySQL para o Banco de Dados do Azure para MySQL online usando o DMS

Use o Serviço de Migração de Banco de Dados do Azure para migrar bancos de dados de uma instância do RDS MySQL para o [Banco de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/), enquanto o banco de dados de origem permanece online durante a migração. Em outras palavras, a migração pode ser feita com o mínimo de tempo de inatividade para o aplicativo. Neste tutorial, você migrará o banco de dados de exemplo **Employees** de uma instância do RDS MySQL para o Banco de Dados do Azure para MySQL usando a atividade de migração online no Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Migrar o esquema de exemplo usando os utilitários mysqldump e mysql.
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Para saber mais, confira a página de [preços](https://azure.microsoft.com/pricing/details/database-migration/) do Serviço de Migração de Banco de Dados do Azure.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como executar uma migração online de uma instância do RDS MySQL para o Banco de Dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Garantir que o servidor de origem do MySQL esteja executando uma edição da comunidade do MySQL compatível. Para determinar a versão da instância do MySQL, no utilitário mysql ou no MySQL Workbench, execute o comando:

    ```
    SELECT @@version;
    ```

    Para obter mais informações, confira o artigo [Supported Azure Database for MySQL versions](https://docs.microsoft.com/azure/mysql/concepts-supported-versions) (Versões compatíveis do Banco de Dados do Azure para MySQL).

* Baixe e instale o [banco de dados de exemplo **Employees** do MySQL](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Crie uma instância do [Banco de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Criar uma VNET (Rede Virtual) do Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma VNet, confira a [Documentação da Rede Virtual](https://docs.microsoft.com/azure/virtual-network/) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.
* Verifique se as regras do Grupo de Segurança de Rede da VNET não bloqueiam as seguintes portas de comunicação de entrada com o Serviço de Migração de Banco de Dados do Azure: 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego do NSG da VNET do Azure, confira o artigo [Filtrar o tráfego de rede com Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o servidor de origem do MySQL, que, por padrão, é a porta TCP 3306.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
* Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível de servidor para o Banco de Dados do Azure para MySQL a fim de permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-redes da VNET usado para o Serviço de Migração de Banco de Dados do Azure.

> [!NOTE]
> O Banco de Dados do Azure para MySQL dá suporte somente a tabelas do InnoDB. Para converter tabelas do MyISAM para o InnoDB, confira o artigo [Como converter tabelas do MyISAM para o InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html).

### <a name="set-up-aws-rds-mysql-for-replication"></a>Configurar o AWS RDS MySQL para replicação

1. Para criar um grupo de parâmetros, siga as instruções fornecidas pelo AWS no artigo [Arquivos de log do banco de dados MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html), na seção **Formato de log binário**.
2. Crie um grupo de parâmetros com a seguinte configuração:
    * binlog_format = row
    * binlog_checksum = NONE
3. Salve o novo grupo de parâmetros.

## <a name="migrate-the-schema"></a>Migrar o esquema

1. Extraia o esquema do banco de dados de origem e aplique-o ao banco de dados de destino para concluir a migração de todos os objetos de banco de dados, como esquemas de tabela, índices e procedimentos armazenados.

    A maneira mais fácil de migrar somente o esquema é usar o mysqldump com o parâmetro --no-data. O comando usado para migrar o esquema é:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Por exemplo, para despejar um arquivo de esquema para o banco de dados **Employees**, use o seguinte comando:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importe o esquema para o serviço de destino, que é o Banco de Dados do Azure para MySQL. Para restaurar o arquivo de despejo de esquema, execute o seguinte comando:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Por exemplo, para importar o esquema para o banco de dados **Employees**:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Se você tiver chaves estrangeiras em seu esquema, o carregamento inicial e a sincronização contínua da migração falharão. Para extrair o script de chave estrangeira de descarte e adicionar o script de chave estrangeira no destino (Banco de Dados do Azure para MySQL), execute o seguinte script no MySQL Workbench:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
        KCU.TABLE_NAME,
        KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ('SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Execute a chave estrangeira de remoção (que é a segunda coluna) no resultado da consulta para remover a chave estrangeira.

5. Se você tiver gatilhos (gatilho de inserção ou de atualização) nos dados, eles imporão a integridade dos dados no destino antes de replicar os dados da origem. A recomendação é desabilitar os gatilhos em todas as tabelas *no destino* durante a migração e, em seguida, habilitar os gatilhos depois que a migração é concluída.

    Para desabilitar os gatilhos no banco de dados de destino:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Se há instâncias do tipo de dados ENUM nas tabelas, recomendamos a atualização temporária para o tipo de dados ‘character varying’ na tabela de destino. Quando a replicação de dados for concluída, reverta o tipo de dados para ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione a localização na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure.

5. Selecione uma VNET existente ou crie uma.

    A VNET fornece ao Serviço de Migração de Banco de Dados do Azure o acesso à instância de origem do MySQL e à instância de destino do Banco de Dados do Azure para MySQL.

    Para obter mais informações sobre como criar uma VNET no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um tipo de preço; para esta migração online, selecione o tipo de preço Premium: 4vCores.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Na tela dos **Serviços de Migração de Banco de Dados do Azure**, procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou e, em seguida, selecione a instância.

     ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **MySQL** e, em seguida, na caixa de texto **Tipo de servidor de destino**, selecione **AzureDbForMySQL**.
5. Na seção **Escolher o tipo de atividade**, selecione **Migração de dados online**.

    > [!IMPORTANT]
    > Verifique se você selecionou **Migração de dados online**; não há suporte para migrações offline para esse cenário.

    ![Criar o Serviço de migração de banco de dados do Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

6. Clique em **Salvar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    > [!NOTE]
    > Anote os pré-requisitos necessários para configurar a migração online na folha de criação do projeto.

## <a name="specify-source-details"></a>Especifique as configurações de origem

* Na tela **Detalhes da origem da migração**, especifique os detalhes de conexão da instância de origem do MySQL.

   ![Detalhes da origem](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Selecione **Salvar** e, em seguida, na tela **Detalhes de destino**, especifique os detalhes de conexão do servidor de destino do Banco de Dados do Azure para MySQL, que é pré-provisionada e tem o esquema **Employees** implantado usando o MySQLDump.

    ![Selecionar o destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Resumo do Aplicativo](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

    A janela de atividade de migração aparece e o **Status** da atividade está **Inicializando**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Em execução**.

    ![Status da atividade – em execução](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Em **NOME DO BANCO DE DADOS**, selecione um banco de dados específico para obter o status de migração das operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    **Carregamento de dados completo** mostra o status de migração da carga inicial, enquanto **Sincronização de dados incremental** mostra o status da CDA (captura de dados de alterações).

    ![Tela Estoque – carregamento de dados completo](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Tela Estoque – sincronização de dados incremental](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do Carregamento completo inicial, os bancos de dados serão marcados como **Prontos para Substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

    ![Iniciar a substituição](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Pare todas as transações de entrada para o banco de dados de origem; aguarde até que o contador **Alterações pendentes** contador mostre **0**.
3. Selecione **Confirmar**e selecione **Aplicar**.
4. Quando o status de migração de banco de dados mostrar **Concluído**, conecte seus aplicativos ao novo banco de dados de destino do Banco de Dados do Azure para MySQL.

A migração online de uma instância local do MySQL para o Banco de Dados do Azure para MySQL agora foi concluída.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre o Banco de Dados do Azure para MySQL, confira o artigo [O que é o Banco de Dados do Azure para MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Em caso de outras dúvidas, envie um email para o alias [Faça uma pergunta às Migrações de Banco de Dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

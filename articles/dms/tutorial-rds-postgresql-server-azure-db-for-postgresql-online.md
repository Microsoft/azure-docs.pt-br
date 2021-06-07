---
title: 'Tutorial: Migrar o PostgreSQL em RDS online para o Banco de Dados do Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Saiba como realizar uma migração online do PostgreSQL de RDS para o Banco de Dados do Azure para PostgreSQL usando o Serviço de Migração de Banco de Dados do Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: c2a6a71365b48fa4349306ce632f5762c38dacf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597463"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Tutorial: Migrar o PostgreSQL em RDS para o Banco de Dados do Azure para PostgreSQL online usando o DMS

É possível usar o Serviço de Migração de Banco de Dados do Azure para migrar bancos de dados de uma instância PostgreSQL de RDS para o [Banco de Dados do Azure para PostgreSQL](../postgresql/index.yml) enquanto o banco de dados de origem permanece online durante a migração. Em outras palavras, a migração pode ser feita com o mínimo de tempo de inatividade para o aplicativo. Neste tutorial, você migrará o banco de dados de exemplo **DVD Rental** para uma instância do PostgreSQL 9.6 de RDS para o Banco de Dados do Azure para PostgreSQL, usando a atividade de migração online no Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Migrar o esquema de exemplo, usando o utilitário pg_dump.
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração usando o Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.
> * Executar a substituição da migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Para saber mais, confira a página de [preços](https://azure.microsoft.com/pricing/details/database-migration/) do Serviço de Migração de Banco de Dados do Azure. Criptografamos o disco para evitar o roubo de dados durante o processo de migração.

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como realizar uma migração online de uma instância local do PostgreSQL para o Banco de Dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Baixe e instale o [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 ou 10. A versão do PostgreSQL Server de origem deve ser 9.5.11, 9.6.7, 10 ou posterior. Para obter mais informações, consulte o artigo [Versões com suporte do Banco de Dados do PostgreSQL](../postgresql/concepts-supported-versions.md).

   Observe também que a versão de destino do Banco de Dados do Azure para PostgreSQL deve ser igual ou posterior à versão do PostgreSQL em RDS. Por exemplo, o PostgreSQL 9.6 em RDS só pode fazer a migração para o Banco de Dados do Azure para PostgreSQL 9.6, 10 ou 11, mas não para o Banco de Dados do Azure para PostgreSQL 9.5.

* Criar uma instância do [Banco de Dados do Azure para PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) ou do [Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)](../postgresql/quickstart-create-hyperscale-portal.md). Veja esta [seção](../postgresql/quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) do documento para obter detalhes sobre como conectar o Servidor PostgreSQL usando pgAdmin.
* Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.
* Verifique se as regras do grupo de segurança de rede da rede virtual não bloqueiam a porta de saída 443 de ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o servidor PostgreSQL de origem, que por padrão é a porta TCP 5432.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
* Crie uma [regra de firewall](../postgresql/concepts-firewall-rules.md) no nível do servidor para que o Banco de Dados do Azure para servidor PostgreSQL permita que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de destino. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Configurar AWS RDS para PostgreSQL para replicação

1. Para criar um novo grupo de parâmetros, siga as instruções fornecidas pelo AWS no artigo [Trabalhar com Grupos de Parâmetros de BD](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Use o nome de usuário mestre para conectar a origem do Serviço de Migração de Banco de Dados do Azure. Se você usar uma conta diferente da conta de usuário mestre, a conta deverá ter a função rds_superuser e a função rds_replication. A função rds_replication concede permissões para gerenciar slots lógicos e para transmitir dados usando slots lógicos.
3. Crie um grupo de parâmetros com a seguinte configuração:

    a. Defina o parâmetro rds.logical_replication no grupo de parâmetros do BD como 1.

    b. max_wal_senders = [número de tarefas simultâneas] - O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas, o recomendável são 10 tarefas.

    c. max_replication_slots = [número de slots]; é recomendável configurar como cinco slots.

4. Associe o grupo de parâmetros que você criou à instância do PostgreSQL de RDS.

## <a name="migrate-the-schema"></a>Migrar o esquema

1. Extraia o esquema do banco de dados de origem e aplique-o ao banco de dados de destino para concluir a migração de todos os objetos de banco de dados, como esquemas de tabela, índices e procedimentos armazenados.

    A maneira mais fácil de migrar apenas o esquema, é usar o pg_dump com a opção -s. Para obter mais informações, consulte os [exemplos](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) no tutorial de pg_dump do Postgres.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para despejar um arquivo de esquema para o banco de dados **dvdrental**, use o comando a seguir:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Crie um banco de dados vazio no serviço de destino, que é o Banco de Dados do Azure para PostgreSQL. Para conectar e criar um banco de dados, consulte um dos artigos a seguir:

    * [Criar um Banco de Dados do Azure para servidor PostgreSQL usando o portal do Azure](../postgresql/quickstart-create-server-database-portal.md)
    * [Criar um servidor de Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus) usando o portal do Azure](../postgresql/quickstart-create-hyperscale-portal.md)

3. Importe o esquema para o serviço de destino, que é o Banco de Dados do Azure para PostgreSQL. Para restaurar o arquivo de despejo de esquema, execute o seguinte comando:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Se você tiver chaves estrangeiras em seu esquema, o carregamento inicial e a sincronização contínua da migração falharão. Para extrair o script de chave estrangeira removível e adicionar o script de chave estrangeira no destino (Banco de Dados do Azure para PostgreSQL), execute o script a seguir no PgAdmin ou no psql:
  
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

5. Execute a chave estrangeira removível (que é a segunda coluna) no resultado da consulta para remover a chave estrangeira.

6. Se você tiver gatilhos (gatilho de inserção ou de atualização) nos dados, eles imporão a integridade dos dados no destino antes de replicar os dados da origem. A recomendação é desabilitar os gatilhos em todas as tabelas *no destino* durante a migração e, em seguida, habilitar os gatilhos depois que a migração é concluída.

    Para desabilitar os gatilhos no banco de dados de destino:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name,' ON ', event_object_table, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome para o serviço, a assinatura e um grupo de recurso novo ou existente.

4. Selecione a localização na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso à instância de origem do PostgreSQL e à instância de destino do Banco de Dados do Azure para PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

6. Selecione um tipo de preço; para esta migração online, selecione o tipo de preço Premium: 4vCores.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Selecione **Criar** para criar a conta.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Crie uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Na tela **Serviços de Migração de Banco de Dados do Azure**, procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou, selecione a instância e escolha + **Novo Projeto de Migração**.
3. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **AWS RDS para PostgreSQL** e, em seguida, na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados do Azure para PostgreSQL**.
4. Na seção **Escolher o tipo de atividade**, selecione **Migração de dados online**.

    > [!IMPORTANT]
    > Verifique se você selecionou **Migração de dados online**; não há suporte para migrações offline para esse cenário.

    ![Criar o Serviço de migração de banco de dados do Azure](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

5. Clique em **Salvar**.

6. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    > [!NOTE]
    > Anote os pré-requisitos necessários para configurar a migração online na folha de criação do projeto.

## <a name="specify-source-details"></a>Especifique as configurações de origem

* Na tela **Adicionar Detalhes da Origem**, especifique os detalhes da conexão para a instância de origem do PostgreSQL.

   ![Detalhes da origem](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Selecione **Salvar** e, em seguida, na tela **Detalhes do destino**, especifique os detalhes da conexão para o Banco de Dados do Azure para servidor PostgreSQL, que é pré-provisionado e tem o esquema **DVD Rentals** implantado usando pg_dump.

    ![Detalhes do destino](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Mapear para bancos de dados de destino](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Resumo do Aplicativo](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

    A janela de atividade de migração aparece e o **Status** da atividade está **Inicializando**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Em execução**.

    ![Status da atividade – em execução](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Em **NOME DO BANCO DE DADOS**, selecione um banco de dados específico para obter o status de migração das operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    **Carregamento de dados completo** mostra o status de migração da carga inicial, enquanto **Sincronização de dados incremental** mostra o status da CDA (captura de dados de alterações).

    ![Tela Estoque – carregamento de dados completo](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Tela Estoque – sincronização de dados incremental](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do Carregamento completo inicial, os bancos de dados serão marcados como **Prontos para Substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

2. Aguarde até que o contador **Alterações pendentes** mostre **0** para que todas as transações de entrada do banco de dados de origem sejam interrompidas, marque a caixa de seleção **Confirmar** e selecione **Aplicar**.

    ![Tela Concluir a substituição](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Quando o status de migração do banco de dados mostrar **Concluído**, conecte os aplicativos ao novo Banco de Dados do Azure para banco de dados PostgreSQL de destino.

A migração online de uma instância local do PostgreSQL em RDS para o Banco de Dados do Azure para PostgreSQL está concluída.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](./dms-overview.md).
* Para obter informações sobre o Banco de Dados do Azure para PostgreSQL, consulte o artigo [O que é o Banco de Dados do Azure para PostgreSQL?](../postgresql/overview.md).
* Em caso de outras dúvidas, envie um email para o alias [Faça uma pergunta às Migrações de Banco de Dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

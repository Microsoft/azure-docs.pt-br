---
title: 'Tutorial: Migrar o PostgreSQL para o BD do Azure para PostgreSQL online por meio do portal do Azure'
titleSuffix: Azure Database Migration Service
description: Saiba como fazer a migração online do PostgreSQL local para o Banco de Dados do Azure para PostgreSQL usando o Serviço de Migração de Banco de Dados do Azure por meio do portal do Azure.
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
ms.openlocfilehash: d28c45b2d0fc1a123f44020f42c4d2c59c593cb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709913"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Tutorial: Migrar o PostgreSQL para o BD do Azure para PostgreSQL online usando o DMS por meio do portal do Azure

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância local do PostgreSQL para o [Banco de Dados do Azure para PostgreSQL](../postgresql/index.yml) com um tempo de inatividade mínimo para o aplicativo. Neste tutorial, você vai migrar o banco de dados de exemplo **DVD Rental** de uma instância local do PostgreSQL 9.6 para o Banco de Dados do Azure para PostgreSQL usando uma atividade de migração online no Serviço de Migração de Banco de Dados do Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
>
> * Migrar o esquema de exemplo usando o utilitário pg_dump.
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Criar um projeto de migração no Serviço de Migração de Banco de Dados do Azure.
> * Executar a migração.
> * Monitorar a migração.
> * Executar a substituição da migração.

> [!NOTE]
> Usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração online exige a criação de uma instância com base no tipo de preço Premium. Criptografamos o disco para evitar o roubo de dados durante o processo de migração

> [!IMPORTANT]
> Para obter uma experiência ideal de migração, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Banco de Dados do Azure na mesma região do Azure como o banco de dados de destino. Mover dados entre regiões ou áreas geográficas pode desacelerar o processo de migração e introduzir erros.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Baixar e instalar o [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 ou 10. A versão do PostgreSQL Server de origem deve ser 9.4, 9.5, 9.6, 10 ou 11. Para obter mais informações, consulte o artigo [Versões com suporte do Banco de Dados do PostgreSQL](../postgresql/concepts-supported-versions.md).

    Observe também que a versão do Banco de Dados de destino do Azure para PostgreSQL precisa ser igual ou posterior à versão local do PostgreSQL. Por exemplo, o PostgreSQL 9.6 pode migrar para o Banco de Dados do Azure para PostgreSQL 9.6, 10 ou 11, mas não para o Banco de Dados do Azure para PostgreSQL 9.5.

* [Criar um servidor de Banco de Dados do Azure para PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) ou [Criar um servidor de Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)](../postgresql/quickstart-create-hyperscale-portal.md).
* Criar uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se você usar o ExpressRoute com emparelhamento de rede com a Microsoft, adicione os seguintes [pontos de extremidade](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade do SQL, ponto de extremidade do Cosmos DB, e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o Serviço de Migração de Banco de Dados do Azure não tem conectividade com a internet.

* Verifique se as regras do NSG (grupo de segurança de rede) para a sua rede virtual não bloqueiam a porta de saída 443 de ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse o servidor PostgreSQL de origem, que por padrão é a porta TCP 5432.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
* Crie uma [regra de firewall](../postgresql/concepts-firewall-rules.md) no nível de servidor para o Banco de Dados do Azure para PostgreSQL a fim de permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.
* Habilite a replicação lógica no arquivo postgresql.config e defina os seguintes parâmetros:

  * wal_level = **lógico**
  * max_replication_slots = [número de slots]; é recomendável configurar como **cinco slots**
  * max_wal_senders =[número de tarefas simultâneas] – O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas; é recomendável definir como **10 tarefas**

> [!IMPORTANT]
> Todas as tabelas do banco de dados existente precisam de uma chave primária para que as alterações possam ser sincronizadas com o banco de dados de destino.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de banco de dados, como procedimentos armazenados, índices e esquemas de tabela, é necessário extrair o esquema do banco de dados de origem e aplicar ao banco de dados.

1. Use o comando pg_dump -s para criar um arquivo de despejo de esquema para um banco de dados.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para criar um arquivo de despejo de esquema para o banco de dados **dvdrental**:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Para obter mais informações sobre como usar o utilitário pg_dump, confira os exemplos do tutorial [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Crie um banco de dados vazio no ambiente de destino, que é o Banco de Dados do Azure para PostgreSQL.

    Para obter detalhes sobre como conectar e criar um banco de dados, confira o artigo [Criar um servidor de Banco de Dados do Azure para PostgreSQL no portal do Azure](../postgresql/quickstart-create-server-database-portal.md) ou [Criar um servidor de Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus) no portal do Azure](../postgresql/quickstart-create-hyperscale-portal.md).

    > [!NOTE]
    > Uma instância do Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus) tem apenas um banco de dados individual: o **citus**.

3. Importe o esquema para o banco de dados de destino criado restaurando o arquivo de despejo do esquema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Para extrair o script de remoção da chave estrangeira e adicioná-lo ao destino (Banco de Dados do Azure para PostgreSQL), execute o script a seguir no PgAdmin ou no psql.

   > [!IMPORTANT]
   > As chaves estrangeiras do esquema causarão a falha do carregamento inicial e da sincronização contínua da migração.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Execute a remoção de chave estrangeira (que é a segunda coluna) no resultado da consulta.

6. Para desabilitar os gatilhos do banco de dados de destino, execute o script a seguir.

   > [!IMPORTANT]
   > Os gatilhos (inserir ou atualizar) dos dados impõem a integridade dos dados no destino antes que os dados sejam replicados da origem. Como resultado, é recomendado que você desabilite os gatilhos em todas as tabelas **no destino** durante a migração e habilite-os novamente após a conclusão da migração.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Entre no portal do Azure, selecione **Todos os serviços** e selecione **Assinaturas**.

   ![Mostrar assinaturas do portal](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome, a assinatura, um grupo de recursos novo ou existente e a localização do serviço.

4. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao servidor de origem do PostgreSQL e à instância do Banco de Dados do Azure para PostgreSQL de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

5. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecione **Examinar + criar** para criar o serviço.

   A criação do serviço será concluída em cerca de 10 a 15 minutos.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na tela **Serviços de Migração de Banco de Dados do Azure**, procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou, selecione a instância e, em seguida, selecione + **Novo Projeto de Migração**.

3. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **PostgreSQL**, na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados do Azure para PostgreSQL**.

4. Na seção **Escolher o tipo de atividade**, selecione **Migração de dados online**.

    ![Criar projeto do Serviço de Migração de Banco de Dados do Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

5. Selecione **Salvar**, observe os requisitos para usar o Serviço de Migração de Banco de Dados do Azure com sucesso a fim de migrar os dados e, em seguida, selecione a **atividade Criar e executar**.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Adicionar Detalhes da Origem**, especifique os detalhes da conexão para a instância de origem do PostgreSQL.

    ![Tela Adicionar Detalhes da Origem](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Detalhes do destino**, especifique os detalhes da conexão para o servidor de destino Hiperescala (Citus), que é a instância pré-provisionada da Hiperescala (Citus) na qual o esquema de **DVD Rentals** foi implantado usando pg_dump.

    ![Tela de detalhes do destino](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Tela Mapear para bancos de dados de destino](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selecione **Salvar** e, em seguida, na tela **Configurações de migração**, aceite os valores padrão.

    ![Tela Configurações de migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Tela Resumo da migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

    A janela de atividade de migração é exibida e o **Status** da atividade deve ser atualizado para **Backup em andamento**.

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Concluído**.

     ![Monitorar o processo de migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Quando a migração for concluída, em **Nome do Banco de Dados**, selecione um banco de dados específico para obter o status de migração das operações **Carregamento de dados completo** e **Sincronização incremental de dados**.

   > [!NOTE]
   > **Carregamento de dados completo** mostra o status de migração da carga inicial, enquanto **Sincronização de dados incremental** mostra o status da CDA (captura de dados de alterações).

     ![Detalhes do carregamento de dados completo](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Detalhes da sincronização incremental de dados](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do carregamento completo inicial, os bancos de dados são marcados como **Pronto para substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

2. Aguarde até que o contador **Alterações pendentes** mostre **0** para que todas as transações de entrada do banco de dados de origem sejam interrompidas, marque a caixa de seleção **Confirmar** e selecione **Aplicar**.

    ![Tela Concluir a substituição](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando o status da migração de banco de dados mostrar **Concluído**, [recrie sequências](https://wiki.postgresql.org/wiki/Fixing_Sequences) (se aplicável) e conecte seus aplicativos à nova instância do Banco de Dados do Azure para PostgreSQL de destino.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados do Azure para PostgreSQL, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](./dms-overview.md).
* Para obter informações sobre o Banco de Dados do Azure para PostgreSQL, consulte o artigo [O que é o Banco de Dados do Azure para PostgreSQL?](../postgresql/overview.md).
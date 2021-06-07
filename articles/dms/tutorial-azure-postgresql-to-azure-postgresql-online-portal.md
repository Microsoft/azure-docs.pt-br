---
title: 'Tutorial: Migrar online o BD do Azure para PostgreSQL para o BD do Azure para PostgreSQL por meio do portal do Azure'
titleSuffix: Azure Database Migration Service
description: Saiba como executar uma migração online de um BD do Azure para PostgreSQL para outro Banco de Dados do Azure para PostgreSQL usando o Serviço de Migração de Banco de Dados do Azure por meio do portal do Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 996523d507f0a4f2850e936df39a38769bc47cde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091300"
---
# <a name="tutorial-migrateupgrade-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>Tutorial: Migrar/Atualizar online o BD do Azure para PostgreSQL – Servidor Único para o BD do Azure para PostgreSQL – Servidor Único usando o DMS por meio do portal do Azure

Você pode usar o Serviço de Migração de Banco de Dados do Azure para migrar os bancos de dados de uma instância do [Banco de Dados do Azure para PostgreSQL – Servidor Único](../postgresql/overview.md#azure-database-for-postgresql---single-server) para uma a mesma versão ou uma versão diferente da instância do Banco de Dados do Azure para PostgreSQL – Servidor Único ou do Banco de Dados do Azure para PostgreSQL – Servidor Flexível com tempo de inatividade mínimo. Neste tutorial, você migrará o banco de dados de exemplo **DVD Rental** de um Banco de Dados do Azure para PostgreSQL v10 para o Banco de Dados do Azure para PostgreSQL – Servidor Único usando a atividade de migração online no Serviço de Migração de Banco de Dados do Azure.

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

> [!IMPORTANT]
> A migração do Banco de Dados do Azure para PostgreSQL é compatível com o PostgreSQL versão 10 e posteriores. Você também pode usar este tutorial para migrar de uma instância do Banco de Dados do Azure para PostgreSQL para outra instância do Banco de Dados do Azure para PostgreSQL ou para uma instância de Hiperescala (Citus).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* Verifique o [status dos cenários de migração compatíveis com o Serviço de Migração de Banco de Dados do Azure](./resource-scenario-status.md) para obter as combinações de migração e versão com suporte. 
* Uma instância existente do [Banco de Dados do Azure para PostgreSQL](../postgresql/index.yml) versão 10 ou posteriores com o banco de dados **DVD Rental**. 

    Observe também que a versão do Banco de Dados do Azure para PostgreSQL de destino precisa ser igual ou posterior à versão local do PostgreSQL. Por exemplo, o PostgreSQL 10 pode migrar para o Banco de Dados do Azure para PostgreSQL 10 ou 11, mas não para o Banco de Dados do Azure para PostgreSQL 9.6.

* [Crie um servidor do Banco de Dados do Azure para PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) ou [Crie um Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)](../postgresql/quickstart-create-hyperscale-portal.md) como o servidor de banco de dados de destino para o qual os dados serão migrados.
* Crie uma Rede Virtual do Microsoft Azure para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager. Para obter mais informações sobre como criar uma rede virtual, confira a [Documentação da Rede Virtual](../virtual-network/index.yml) e, especificamente, os artigos de Início Rápido com detalhes passo a passo.

* Verifique se as regras do NSG (grupo de segurança de rede) para a sua rede virtual não bloqueiam a porta de saída 443 de ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego do NSG da rede virtual, confira o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Crie uma [regra de firewall](../azure-sql/database/firewall-configure.md) no nível do servidor para que o Banco de Dados do Azure para PostgreSQL de origem permita que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.
* Crie uma [regra de firewall](../azure-sql/database/firewall-configure.md) no nível do servidor para que o Banco de Dados do Azure para PostgreSQL de destino permita que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de destino. Forneça o intervalo de sub-redes da rede virtual usado para o Serviço de Migração de Banco de Dados do Azure.
* [Habilitar replicação lógica](../postgresql/concepts-logical.md) na origem do BD do Azure para PostgreSQL. 
* Defina os seguintes parâmetros do servidor na instância do Banco de Dados do Azure para PostgreSQL que está sendo usada como uma origem:

  * max_replication_slots = [número de slots]; é recomendável definir como **dez slots**
  * max_wal_senders =[número de tarefas simultâneas] – O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas; é recomendável definir como **10 tarefas**

> [!NOTE]
> Os parâmetros do servidor acima são estáticos e exigirão uma reinicialização da instância do Banco de Dados do Azure para PostgreSQL para que entrem em vigor. Para obter mais informações sobre como mudar os parâmetros do servidor, confira [Configurar os parâmetros do Banco de Dados do Azure para PostgreSQL](../postgresql/howto-configure-server-parameters-using-portal.md).

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
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
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
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Para extrair o script de remoção da chave estrangeira e adicioná-lo ao destino (Banco de Dados do Azure para PostgreSQL), execute o script a seguir no PgAdmin ou no psql.

   > [!IMPORTANT]
   > As chaves estrangeiras do esquema causarão a falha do carregamento inicial e da sincronização contínua da migração.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name ,'"', STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ','"', foreignkey,'"'), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name,'"', STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ','"', foreignkey,'"', ' FOREIGN KEY (','"', column_name,'"', ')', ' REFERENCES ','"', foreign_table_schema,'"', '.','"', foreign_table_name,'"', '(','"', foreign_column_name,'"', ')',' ON UPDATE ',update_rule,' ON DELETE ',delete_rule), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name,
        S.update_rule,
        S.delete_rule
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name,
        rc.update_rule AS update_rule,
        rc.delete_rule AS delete_rule
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
        JOIN information_schema.referential_constraints as rc ON rc.constraint_name = tc.constraint_name AND rc.constraint_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name,S.update_rule,S.delete_rule
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

   ![Mostrar assinaturas do portal](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecione a assinatura na qual deseja criar a instância do Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Provedores de recursos**.

    ![Exibir provedores de recursos](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Pesquise por migração e, em seguida, à direita do **Microsoft.DataMigration**, selecione **Registrar**.

    ![Registrar provedor de recursos](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, pesquise Serviço de Migração de Banco de Dados do Azure e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na tela **Serviço de Migração de Banco de Dados do Azure**, selecione **Criar**.

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na tela **Criar Serviço de Migração**, especifique um nome, a assinatura, um grupo de recursos novo ou existente e a localização do serviço.

4. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao servidor de origem do PostgreSQL e à instância do Banco de Dados do Azure para PostgreSQL de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md).

5. Selecione um tipo de preço.

    Para obter mais informações sobre os custos e camadas de preços, consulte a [página de preços](https://aka.ms/dms-pricing).

    ![Criar uma instância do Serviço de Migração de Banco de Dados do Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecione **Examinar + criar** para criar o serviço.

   A criação do serviço será concluída em cerca de 10 a 15 minutos.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois que o serviço é criado, localize-o no portal do Azure, abra-o e, em seguida, crie um projeto de migração.

1. Faça logon no portal do Azure, selecione **+ criar um recurso**, procure o serviço de migração de banco de dados do Azure e, em seguida, selecione **serviço de migração de banco de dados do Azure** na lista suspensa.

      ![Localize todas as instâncias do Serviço de Migração de Banco de Dados do Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na tela **Serviços de Migração de Banco de Dados do Azure**, procure o nome da instância do Serviço de Migração de Banco de Dados do Azure que você criou, selecione a instância e, em seguida, selecione + **Novo Projeto de Migração**.

3. Na tela **Novo projeto de migração**, especifique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **PostgreSQL**, na caixa de texto **Tipo de servidor de destino**, selecione **Banco de Dados do Azure para PostgreSQL**.
    > [!NOTE]
    > Escolha **PostgreSQL** no **Tipo de servidor de origem**, mesmo que o servidor de origem seja uma instância do **Banco de Dados do Azure para PostgreSQL**.  

4. Na seção **Escolher o tipo de atividade**, selecione **Migração de dados online**.

    ![Criar projeto do Serviço de Migração de Banco de Dados do Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **Criar somente o projeto** para criar o projeto de migração agora e executar a migração posteriormente.

5. Selecione **Salvar**, observe os requisitos para usar o Serviço de Migração de Banco de Dados do Azure com sucesso a fim de migrar os dados e, em seguida, selecione a **atividade Criar e executar**.

## <a name="specify-source-details"></a>Especifique as configurações de origem

1. Na tela **Adicionar Detalhes da Origem**, especifique os detalhes da conexão para a instância de origem do PostgreSQL.

    ![Tela Adicionar Detalhes da Origem](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Encontre detalhes, como "Nome do servidor", "Porta do servidor", "Nome do banco de dados" etc., no portal do **Banco de Dados do Azure para PostgreSQL**.

2. Clique em **Salvar**.

## <a name="specify-target-details"></a>Detalhes do destino favorito

1. Na tela **Detalhes do destino**, especifique os detalhes da conexão para o servidor de destino Hiperescala (Citus), que é a instância pré-provisionada da Hiperescala (Citus) na qual o esquema de **DVD Rentals** foi implantado usando pg_dump.

    ![Tela de detalhes do destino](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Você pode migrar de uma instância do Banco de Dados do Azure para PostgreSQL para outra instância do Banco de Dados do Azure para PostgreSQL de servidor único ou para um servidor de Hiperescala (Citus).

2. Selecione **Salvar** e, na tela **Mapear para bancos de dados de destino**, mapeie os bancos de dados de origem e de destino para a migração.

    Se o banco de dados de destino contiver o mesmo nome de banco de dados do banco de dados de origem, o Serviço de Migração de Banco de Dados do Azure selecionará o banco de dados de destino por padrão.

    ![Tela Mapear para bancos de dados de destino](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selecione **Salvar** e, em seguida, na tela **Configurações de migração**, aceite os valores padrão.

    ![Tela Configurações de migração](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecione **Salvar** na tela **Resumo de migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e reveja o resumo para ter certeza de que os detalhes de origem e destino correspondem ao que foi especificado anteriormente.

    ![Tela Resumo da migração](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Execute a migração

* Selecione **Executar migração**.

A janela de atividade de migração é exibida e o **Status** da atividade deve ser atualizado para **Backup em andamento**. Você poderá encontrar o seguinte erro ao atualizar do BD do Azure para PostgreSQL 9.5 ou 9.6:

**Um cenário relatou um erro desconhecido. 28000: sem entrada pg_hba.conf para conexão de replicação do host "40.121.141.121", usuário "sr"**

Isso ocorre porque o PostgreSQL não tem os privilégios apropriados para criar artefatos de replicação lógica necessários. Para habilitar os privilégios necessários, você pode fazer o seguinte:

1. Abra as configurações de "Segurança de conexão" para o servidor de origem do BD do Azure para PostgreSQL do qual você está tentando fazer a migração/atualização.
2. Adicione uma nova regra de firewall com um nome que termina com "_replrule" e adicione o endereço IP da mensagem de erro nos campos IP inicial e IP final. Para o exemplo de erro acima –
> Nome de regra de firewall = sr_replrule; IP inicial = 40.121.141.121; IP final = 40.121.141.121

3. Clique em salvar e espere a alteração ser concluída. 
4. Repita a atividade do DMS. 

## <a name="monitor-the-migration"></a>Monitorar a migração

1. Na tela de atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **Status** da migração seja exibido como **Concluído**.

     ![Monitorar o processo de migração](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Quando a migração for concluída, em **Nome do Banco de Dados**, selecione um banco de dados específico para obter o status de migração das operações **Carregamento de dados completo** e **Sincronização incremental de dados**.

   > [!NOTE]
   > **Carregamento de dados completo** mostra o status de migração da carga inicial, enquanto **Sincronização de dados incremental** mostra o status da CDA (captura de dados de alterações).

     ![Detalhes do carregamento de dados completo](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Detalhes da sincronização incremental de dados](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Executar migração de substituição

Após a conclusão do carregamento completo inicial, os bancos de dados são marcados como **Pronto para substituição**.

1. Quando estiver pronto para concluir a migração de banco de dados, selecione **Iniciar substituição**.

2. Aguarde até que o contador **Alterações pendentes** mostre **0** para que todas as transações de entrada do banco de dados de origem sejam interrompidas, marque a caixa de seleção **Confirmar** e selecione **Aplicar**.

    ![Tela Concluir a substituição](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando o status da migração de banco de dados mostrar **Concluído**, [recrie sequências](https://wiki.postgresql.org/wiki/Fixing_Sequences) (se aplicável) e conecte seus aplicativos à nova instância do Banco de Dados do Azure para PostgreSQL de destino.
 
> [!NOTE]
> O Serviço de Migração de Banco de Dados do Azure pode ser usado para executar atualizações de versão principal com tempo de inatividade reduzido no Banco de Dados do Azure para PostgreSQL – Servidor Único. Primeiro, configure um banco de dados de destino com a versão desejada mais alta do PostgreSQL, configurações de rede e parâmetros. Em seguida, você pode iniciar a migração para os bancos de dados de destino usando o procedimento explicado acima. Depois de fazer a transição para o servidor de banco de dados de destino, você pode atualizar a cadeia de conexão do aplicativo para apontar para o servidor de banco de dados de destino. 

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre problemas conhecidos e limitações na realização de migrações online para o Banco de Dados do Azure para PostgreSQL, confira o artigo [Problemas conhecidos e soluções alternativas nas migrações online de Banco de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Serviço de Migração de Banco de Dados do Azure, consulte o artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](./dms-overview.md).
* Para obter informações sobre o Banco de Dados do Azure para PostgreSQL, consulte o artigo [O que é o Banco de Dados do Azure para PostgreSQL?](../postgresql/overview.md).

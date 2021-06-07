---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854068"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Visão geral do aplicativo":::

Este diagrama ilustra o fluxo de trabalho da solução que você cria neste tutorial: 

1. Dados enviados para um hub de eventos do Azure são capturados no Armazenamento de Blobs do Azure.
2. Quando a captura de dados for concluída, um evento será gerado e enviado para uma Grade de Eventos do Azure. 
3. A grade de eventos encaminha esses dados de evento para um aplicativo de funções do Azure.
4. O aplicativo de funções usa a URL do blob nos dados do evento para recuperar o blob do armazenamento. 
5. O aplicativo de funções migra os dados de blob para um Azure Synapse Analytics. 

Neste artigo, você executa as seguintes etapas:

> [!div class="checklist"]
> - Implantar a infraestrutura necessária para o tutorial
> - Publicar o código em um aplicativo do Functions
> - Criar uma assinatura na Grade de Eventos 
> - Transmitir dados de exemplo aos Hubs de Eventos
> - Verificar dados capturados no Azure Synapse Analytics

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você deve ter:

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio 2019](https://www.visualstudio.com/vs/) com cargas de trabalho para: desenvolvimento de área de trabalho do .NET, desenvolvimento do Azure, ASP.NET e desenvolvimento para a Web, desenvolvimento em Node.js e desenvolvimento do Python.
- Baixe o [projeto de exemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) para seu computador.
    - WindTurbineDataGenerator – um editor simples que envia dados de exemplo de turbina eólica para um hub de eventos habilitado para captura
    - FunctionDWDumper: uma função do Azure que recebe uma notificação da Grade de Eventos quando um arquivo Avro é capturado no blob do Armazenamento do Azure. Ela recebe o caminho do URI do blob, lê o conteúdo dele e efetua push desses dados para o Azure Synapse Analytics (pool de SQL dedicado).

## <a name="deploy-the-infrastructure"></a>Implantar a infraestrutura
Nesta etapa, você implanta a infraestrutura necessária com um [modelo do Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Quando você implanta o modelo, são criados os seguintes recursos:

* Hub de Eventos com o recurso Captura habilitado.
* Conta de armazenamento para os arquivos capturados. 
* Plano de serviço de aplicativo para hospedar o aplicativo de função
* Aplicativo de funções para o processamento do evento
* SQL Server para hospedagem do data warehouse
* Azure Synapse Analytics (pool de SQL dedicado) para armazenar os dados migrados

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Usar a CLI do Azure para implantar a infraestrutura

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Selecione o botão **Cloud Shell** na parte superior.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Portal do Azure":::
3. Você verá que o Cloud Shell será aberto na parte inferior do navegador.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. No Cloud Shell, se você vir uma opção para selecionar entre **Bash** e **PowerShell**, selecione **Bash**. 
5. Se você estiver usando o Cloud Shell pela primeira vez, crie uma conta de armazenamento selecionando **Criar armazenamento**. O Azure Cloud Shell requer uma conta de armazenamento do Azure para armazenar alguns arquivos. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Criar armazenamento para o Cloud Shell":::
6. Aguarde até o Cloud Shell ser inicializado. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell inicializado":::
1. Crie um grupo de recursos do Azure executando o seguinte comando da CLI: 
    1. Copie e cole o seguinte comando na janela do Cloud Shell. Altere o nome e a localização do grupo de recursos, se desejar.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Pressione **ENTER**. 

        Veja um exemplo:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Implante todos os recursos mencionados na seção anterior (hub de eventos, conta de armazenamento, aplicativo de funções, o Azure Synapse Analytics) executando o seguinte comando da CLI: 
    1. Copie e cole o comando na janela do Cloud Shell. Como alternativa, talvez você queira copiar/colar em um editor de sua escolha, definir valores e, em seguida, copiar o comando para o Cloud Shell. 

        > [!IMPORTANT]
        > Especifique valores para as seguintes entidades antes de executar o comando: 
        > - Nome do grupo de recursos criado anteriormente.
        > - Nome para o namespace do hub de eventos. 
        > - Nome do hub de eventos. Você pode deixar o valor como está (hubdatamigration).
        > - Nome para o SQL Server.
        > - Nome do usuário do SQL e senha. 
        > - Nome para o banco de dados.
        > - Nome da conta de armazenamento. 
        > - Nome do aplicativo de funções. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Pressione **ENTER** na janela do Cloud Shell para executar o comando. Esse processo pode demorar um pouco, pois você está criando diversos recursos. No resultado do comando, verifique se não houve falhas. 
1. Feche o Cloud Shell selecionando o botão **Cloud Shell** no portal (ou) o botão **X** no canto superior direito da janela do Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Verifique se os recursos são criados

1. No portal do Azure, selecione **Grupos de recursos** no menu esquerdo. 
2. Filtre a lista de grupos de recursos digitando o nome do seu grupo de recursos na caixa de pesquisa. 
3. Selecione seu grupo de recursos na lista.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Selecione o grupo de recursos":::
4. Confirme que você vê os seguintes recursos no grupo de recursos:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Recursos no grupo de recursos" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Criar uma tabela no Azure Synapse Analytics
Crie uma tabela no data warehouse executando o script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Para executar o script, você pode usar o Visual Studio ou o Editor de Consultas no portal. As etapas a seguir mostram como usar o Editor de Consultas: 

1. Na lista de recursos no grupo de recursos, selecione seu **pool de SQL dedicado**. 
2. Na página **Pool de SQL dedicado**, na seção **Tarefas Comuns** no menu à esquerda, selecione **Editor de consultas (versão prévia)** . 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Página do Azure Synapse Analytics":::
2. Insira o nome de **usuário** e **senha** para o SQL Server e selecione **OK**. Se você vir uma mensagem sobre permitir que o cliente acesse o SQL Server, siga estas etapas:
    1. Selecione o link: **Definir firewall do servidor**. 
    2. Na página **Configurações do firewall**, selecione **Adicionar IP do cliente** na barra de ferramentas e selecione **Salvar** na barra de ferramentas. 
    3. Selecione **OK** na mensagem de sucesso.
    4. Navegue de volta para a página **Pool de SQL dedicado** e selecione **Editor de consultas (versão prévia)** no menu à esquerda. 
    5. Insira o **usuário** e a **senha** e selecione **OK**. 
1. Na janela de consulta, copie e execute o seguinte script SQL: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="Executar uma consulta SQL":::
5. Mantenha essa guia ou janela aberta para que você possa verificar se os dados são criados no final do tutorial. 

### <a name="update-the-function-runtime-version"></a>Atualizar a versão de runtime da função

1. Abra outra guia no navegador da Web e navegue até o [portal do Azure](https://portal.azure.com).
1. No portal do Azure, selecione **Grupos de recursos** no menu esquerdo.
1. Selecione o grupo de recursos no qual o aplicativo de funções está localizado. 
1. Selecione o **aplicativo de funções** na lista de recursos no grupo de recursos.
1. Selecione **Configuração** em **Configurações** no menu à esquerda. 
1. Alterne para a guia **Configurações de runtime da função** no painel direito. 
1. Atualize a **versão de runtime** para **~3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="Atualizar a versão de runtime da função":::
6. Selecione **Salvar** na barra de ferramentas. 
1. No pop-up de confirmação **Salvar alterações**, selecione **Continuar**. 

## <a name="publish-the-azure-functions-app"></a>Publicar o aplicativo do Azure Functions

1. Inicie o Visual Studio.
2. Abra a solução **EventHubsCaptureEventGridDemo.sln** que você baixou do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) como parte dos pré-requisitos. Você pode encontrá-la na pasta `/samples/e2e/EventHubsCaptureEventGridDemo`. 
3. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **FunctionEGDWDumper** e selecione **Publicar**.
4. Se você vir a tela a seguir, selecione **Iniciar**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Botão Iniciar na seção Publicar.":::
5. Na caixa de diálogo **Publicar**, selecione **Azure** como **Destino** e **Avançar**. 
6. Selecione **Aplicativo de Funções do Azure (Windows)** e **Avançar**.
7. Na guia **Instância do Functions**, selecione sua assinatura do Azure, expanda o grupo de recursos, escolha o aplicativo de funções e selecione **Concluir**. Faça logon na sua conta do Azure se ainda não fez isso. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Selecione seu aplicativo de funções":::
8. Na página **Publicar**, na seção **Dependências do Serviço**, selecione **Configurar** para **Armazenamento**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Seleção do link Configurar para a dependência do serviço de armazenamento":::
1. Na página **Configurar dependência**, siga estas etapas: 
    1. selecione a **conta de armazenamento** criada anteriormente e selecione **Avançar**. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Escolher conta de armazenamento":::
    10. Especifique um **nome para a cadeia de conexão** e selecione **Nenhum** para a opção **Salvar cadeia de conexão** e escolha **Avançar**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Especificar o nome da cadeia de conexão":::      
    1. Desmarque as opções **Arquivo de código C#** e **Repositório de segredos** e selecione **Concluir**.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Examinar o resumo de alterações":::
1. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Selecionar Publicar":::
2. Na guia em que a página **Função do Azure** está aberta, selecione **Funções** no menu à esquerda. Confirme se a função **EventGridTriggerMigrateData** aparece na lista. Se não a vir, tente publicar no Visual Studio novamente e atualize a página no portal. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Confirmar a criação da função":::    

Depois de publicar a função, você estará pronto para assinar o evento.

## <a name="subscribe-to-the-event"></a>Assinar o evento

1. Em uma nova guia ou em uma nova janela de um navegador da Web, navegue até o [portal do Azure](https://portal.azure.com).
2. No portal do Azure, selecione **Grupos de recursos** no menu esquerdo. 
3. Filtre a lista de grupos de recursos digitando o nome do seu grupo de recursos na caixa de pesquisa. 
4. Selecione seu grupo de recursos na lista.
1. Selecione o **namespace dos Hubs de Eventos** na lista de recursos.
1. Na página **Namespace dos Hubs de Eventos**, selecione **Eventos** no menu à esquerda e selecione **+ Assinatura de Evento** na barra de ferramentas. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Adicionar link de assinatura do evento na página de Eventos para um namespace dos Hubs de Eventos":::
1. Na página **Criar Assinatura de Eventos**, siga estas etapas:
    1. Insira um nome para a **assinatura de evento**. 
    1. Insira um nome para o **tópico do sistema**. Um tópico do sistema fornece um ponto de extremidade para que o remetente envie eventos. Para saber mais, confira [Tópicos do sistema](../articles/event-grid/system-topics.md)
    1. Para o **Tipo de ponto de extremidade**, selecione **Função do Azure**.
    1. Para **Ponto de extremidade**, selecione o link.
    1. Na página **Selecionar Função do Azure**, siga estas etapas se elas não forem realizadas automaticamente.
        1. Selecione a assinatura do Azure que tem a função do Azure. 
        1. Selecione o grupo de recursos para a função. 
        1. Selecione o aplicativo de funções.
        1. Selecione o slot de implantação. 
        1. Selecione a função **EventGridTriggerMigrateData**. 
    1. Na página **Selecionar Função do Azure**, selecione **Confirmar Seleção**.
    1. Em seguida, volte para a página **Criar Assinatura de Evento** e selecione **Criar**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Criar uma assinatura de evento usando a função" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Verifique se a assinatura do evento foi criada. Alterne para a guia **Assinaturas de Evento** na página **Eventos** para o namespace dos Hubs de Eventos. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Confirmar a assinatura do evento" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Selecione o Plano do Serviço de Aplicativo (não o Serviço de Aplicativo) na lista de recursos no grupo de recursos. 

## <a name="run-the-app-to-generate-data"></a>Executar o aplicativo para gerar dados
Você terminou de configurar o hub de eventos, o pool de SQL dedicado (antigo SQL Data Warehouse), o aplicativo de funções do Azure e a assinatura de evento. Antes de executar um aplicativo que gere dados para o hub de eventos, você precisa configurar alguns valores.

1. No portal do Azure, navegue até o grupo de recursos como fez anteriormente. 
2. Selecione o namespace Hubs de Eventos.
3. Na página **Namespace de Hubs de Eventos**, selecione **Políticas de acesso compartilhado** no menu à esquerda.
4. Selecione **RootManageSharedAccessKey** na lista de políticas. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Página de políticas de acesso compartilhado para um namespace dos Hubs de Eventos":::    
1. Selecione o botão Copiar ao lado da caixa de texto **Chave primária da cadeia de conexão**. 
1. Volte para sua solução do Visual Studio. 
1. Clique com o botão direito do mouse no projeto **WindTurbineDataGenerator** e selecione **Definir como projeto de inicialização**. 
1. No projeto WindTurbineDataGenerator, abra **program.cs**.
1. Substitua `<EVENT HUBS NAMESPACE CONNECTION STRING>` pela cadeia de conexão copiada do portal. 
1. Substitua `<EVENT HUB NAME>` pelo nome do hub de eventos. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Compile a solução. Execute o aplicativo **WindTurbineGenerator.exe**. 
7. Após alguns minutos, na outra guia do navegador em que você tem a janela de consulta aberta, consulte a tabela no data warehouse com os dados migrados.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Resultados da consulta](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>Monitorar a solução
Esta seção ajuda você com o monitoramento ou a solução de problemas da solução. 

### <a name="view-captured-data-in-the-storage-account"></a>Exibir dados capturados na conta de armazenamento
1. Navegue até o grupo de recursos e selecione a conta de armazenamento usada para capturar dados de evento. 
1. Na página **Conta de armazenamento**, selecione **Gerenciador de Armazenamento (versão prévia**) no menu esquerdo.
1. Expanda **CONTÊINERES DE BLOB** e selecione **windturbinecapture**. 
1. Abra a pasta com o mesmo nome que o seu **Namespace dos Hubs de Eventos** no painel direito. 
1. Abra a pasta com o mesmo nome que o seu hub de eventos (**hubdatamigration**). 
1. Execute uma consulta drill-through das pastas e veja os arquivos AVRO. Veja um exemplo:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Arquivo capturado no armazenamento" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Verifique se o gatilho da Grade de Eventos invocou a função
1. Navegue até o grupo de recursos e selecione o aplicativo de funções. 
1. Selecione **Funções** no menu esquerdo.
1. Selecione a função **EventGridTriggerMigrateData** na lista. 
1. Na página **Função**, selecione **Monitor** no menu esquerdo. 
1. Selecione **Configurar** para configurar o Application Insights para capturar logs de invocação. 
1. Crie um recurso do **Application Insights** ou use um existente. 
1. Navegue de volta para a página **Monitor** da função. 
1. Confirme se o aplicativo cliente (**WindTurbineDataGenerator**) que está enviando os eventos ainda está em execução. Se não estiver, execute o aplicativo. 
1. Aguarde alguns minutos (5 minutos ou mais) e selecione o botão **Atualizar** para ver as invocações de função.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Invocações de função":::
1. Selecione uma invocação para ver os detalhes.

    A Grade de Eventos distribui os dados do evento para os assinantes. O exemplo a seguir mostra os dados de evento gerados quando os dados sendo transmitidos por um hub de eventos são capturados em um blob. Em particular, observe se a propriedade `fileUrl` no objeto `data` aponta para o blob no armazenamento. O aplicativo de funções usa essa URL para recuperar o arquivo de blob com os dados capturados.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Verifique se os dados estão armazenados no pool de SQL dedicado
Na guia do navegador em que você tem a janela de consulta aberta, consulte a tabela no pool de SQL dedicado para os dados migrados.

![Resultados da consulta](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)


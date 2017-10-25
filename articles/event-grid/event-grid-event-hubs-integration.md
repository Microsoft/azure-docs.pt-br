---
title: "Integração da Grade de Eventos do Azure com os Hubs de Eventos"
description: Descreve como usar a Grade de Eventos do Azure e os Hubs de Eventos para migrar dados para um SQL Data Warehouse
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: f7d2b1970cb7b1330b3d9bdff7987a90fa381392
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Transmitir Big Data para um data warehouse

A [Grade de Eventos](overview.md) do Azure é um serviço de roteamento de evento inteligente que permite que você reaja às notificações de aplicativos e serviços. O [exemplo Captura dos Hubs de Eventos e Grade de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) mostra como usar a Captura dos Hubs de Eventos do Azure com a Grade de Eventos do Azure para migrar dados de um hub de eventos para um SQL Data Warehouse com perfeição.

![Visão geral do aplicativo](media/event-grid-event-hubs-integration/overview.png)

Como os dados são enviados para o hub de eventos, a Captura efetua pull dos dados do fluxo e gera os blobs de armazenamento com os dados no formato Avro. Ao gerar o blob, a Captura dispara um evento. A Grade de Eventos distribui os dados sobre o evento aos assinantes. Nesse caso, os dados do evento são enviados para o ponto de extremidade do Azure Functions. Os dados do evento incluem o caminho do blob gerado. A função usa essa URL para recuperar o arquivo e enviá-lo para o data warehouse.

Neste artigo você:

* Implanta a seguinte infraestrutura:
  * Hub de eventos com a Captura habilitada
  * Conta de armazenamento para os arquivos da Captura
  * Plano de serviço de aplicativo do Azure para hospedar o aplicativo de função
  * Aplicativo de funções para o processamento do evento
  * SQL Server para hospedagem do data warehouse
  * SQL Data Warehouse para armazenamento dos dados migrados
* Cria uma tabela no data warehouse
* Adiciona código ao aplicativo de funções
* Assinar o evento
* Executa o aplicativo que envia dados para o hub de eventos
* Exibe os dados migrados no data warehouse

## <a name="about-the-event-data"></a>Sobre os dados do evento

A Grade de Eventos distribui os dados do evento para os assinantes. O exemplo a seguir mostra os dados de evento para a criação de um arquivo de Captura. Em particular, observe a propriedade `fileUrl` no objeto `data`. O aplicativo de funções obtém esse valor e o utiliza para recuperar o arquivo de Captura.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [Visual Studio 2017 Versão 15.3.2 ou superior](https://www.visualstudio.com/vs/) com cargas de trabalho para: desenvolvimento de área de trabalho do .NET, desenvolvimento do Azure, ASP.NET e desenvolvimento para a Web, desenvolvimento em Node.js e desenvolvimento do Python.
* O [projeto de exemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) baixado em seu computador.

## <a name="deploy-the-infrastructure"></a>Implantar a infraestrutura

Para simplificar este artigo, você implanta a infraestrutura necessária com um modelo do Resource Manager. Para ver os recursos que são implantados, exiba o [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Para a versão prévia, a Grade de Eventos dá suporte às regiões **westus2** e **westcentralus**. Use uma dessas regiões como o local do grupo de recursos.

Para a CLI do Azure, use:

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

Para o PowerShell, use:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

Forneça um valor de senha quando solicitado.

## <a name="create-a-table-in-sql-data-warehouse"></a>Criar uma tabela no SQL Data Warehouse

Adicione uma tabela ao data warehouse, executando o script [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Para executar o script, use o Visual Studio ou o Editor de Consultas no portal.

O script a ser executado é:

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

## <a name="publish-the-azure-functions-app"></a>Publicar o aplicativo do Azure Functions

1. Abra o [projeto de exemplo EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) no Visual Studio 2017 (15.3.2 ou superior).

2. No Gerenciador de Soluções, clique com o botão direito do mouse em **FunctionDWDumper** e selecione **Publicar**.

   ![Publicar o aplicativo de funções](media/event-grid-event-hubs-integration/publish-function-app.png)

3. Selecione **Aplicativo Azure Function** e **Selecionar Existente**. Selecione **OK**.

   ![Destinar aplicativo de funções](media/event-grid-event-hubs-integration/pick-target.png)

4. Selecione o aplicativo de funções implantado por meio do modelo. Selecione **OK**.

   ![Selecionar aplicativo de funções](media/event-grid-event-hubs-integration/select-function-app.png)

5. Quando o Visual Studio tiver configurado o perfil, selecione **Publicar**.

   ![Selecionar Publicar](media/event-grid-event-hubs-integration/select-publish.png)

6. Depois de publicar a função, acesse o [Portal do Azure](https://portal.azure.com/). Selecione o grupo de recursos e o aplicativo de funções.

   ![Exibir aplicativo de funções](media/event-grid-event-hubs-integration/view-function-app.png)

7. Selecione a função.

   ![Selecionar função](media/event-grid-event-hubs-integration/select-function.png)

8. Obtenha a URL para a função. Você precisará desta URL ao criar a assinatura de evento.

   ![Obter URL de função](media/event-grid-event-hubs-integration/get-function-url.png)

9. Copie o valor.

   ![Copiar URL](media/event-grid-event-hubs-integration/copy-url.png)

## <a name="subscribe-to-the-event"></a>Assinar o evento

Você pode usar a CLI do Azure ou o portal para assinar o evento. Este artigo mostra as duas abordagens.

### <a name="portal"></a>Portal

1. No namespace dos Hubs de Eventos, selecione **Grade de Eventos** à esquerda.

   ![Selecionar a Grade de Eventos](media/event-grid-event-hubs-integration/select-event-grid.png)

2. Adicione uma assinatura de evento.

   ![Adicionar assinatura de evento](media/event-grid-event-hubs-integration/add-event-subscription.png)

3. Forneça valores para a assinatura de evento. Use a URL do Azure Functions que você copiou. Selecione **Criar**.

   ![Fornecer valores de assinatura](media/event-grid-event-hubs-integration/provide-values.png)

### <a name="azure-cli"></a>CLI do Azure

Para assinar o evento, execute o seguinte comando:

```azurecli-interactive
az eventgrid resource event-subscription create -g rgDataMigrationSample --provider-namespace Microsoft.EventHub --resource-type namespaces --resource-name <your-EventHubs-namespace> --name captureEventSub --endpoint <your-function-endpoint>
```

## <a name="run-the-app-to-generate-data"></a>Executar o aplicativo para gerar dados

Você terminou de configurar o hub de eventos, o SQL Data Warehouse, o aplicativo de funções do Azure e a assinatura de evento. A solução está pronta para migrar dados do hub de eventos para o data warehouse. Antes de executar um aplicativo que gere dados para o hub de eventos, você precisa configurar alguns valores.

1. No portal, selecione seu namespace do hub de eventos. Selecione **Cadeias de Conexão**.

   ![Selecionar cadeias de conexão](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Selecione **RootManageSharedAccessKey**

   ![Selecionar a chave](media/event-grid-event-hubs-integration/show-root-key.png)

3. Copie **Cadeia de conexão – Chave primária**

   ![Copiar chave](media/event-grid-event-hubs-integration/copy-key.png)

4. Volte ao seu projeto do Visual Studio. No projeto WindTurbineDataGenerator, abra **program.cs**.

5. Substitua os dois valores de constantes. Use o valor copiado para **EventHubConnectionString**. Use o nome do hub de eventos para **EventHubName**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://tfdatamigratens.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Compilar a solução. Execute o aplicativo WindTurbineGenerator.exe. Depois de alguns minutos, consulte os dados migrados na tabela em seu data warehouse.

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para uma introdução à Captura dos Hubs de Eventos, consulte [Habilitar a Captura dos Hubs de Eventos usando o Portal do Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Para obter mais informações sobre como configurar e executar o exemplo, consulte [exemplo Captura dos Hubs de Eventos e Grade de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
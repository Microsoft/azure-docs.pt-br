---
title: Publicar, assinar eventos na nuvem – grade de eventos do Azure IoT Edge | Microsoft Docs
description: Publicar, assinar eventos na nuvem usando o webhook com a grade de eventos no IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: ec7ecb77d37ed1cdf1d13aa7191f5d50e0008c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790792"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Tutorial: publicar, assinar eventos na nuvem

Este artigo percorre todas as etapas necessárias para publicar e assinar eventos usando a grade de eventos no IoT Edge. Este tutorial usa o e o Azure function como o manipulador de eventos. Para obter tipos de destino adicionais, consulte [manipuladores de eventos](event-handlers.md).

Consulte [conceitos da grade de eventos](concepts.md) para entender o que é um tópico e uma assinatura da grade de eventos antes de continuar.

## <a name="prerequisites"></a>Pré-requisitos 
Para concluir este tutorial, você precisará de:

* **Assinatura do Azure** – crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Hub IOT do Azure e IOT Edge dispositivo** -siga as etapas no início rápido para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Criar uma função do Azure no portal do Azure

Siga as etapas descritas no [tutorial](../../azure-functions/functions-get-started.md) para criar uma função do Azure. 

Substitua o trecho de código pelo código a seguir:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

Em sua nova função, selecione **obter URL da função** no canto superior direito, selecione padrão (**chave de função**) e, em seguida, selecione **copiar**. Você usará o valor da URL da função posteriormente no tutorial.

> [!NOTE]
> Consulte a documentação do [Azure Functions](../../azure-functions/functions-overview.md) para obter mais exemplos e tutoriais sobre como reagir a eventos usando gatilhos de evento EventGrid.

## <a name="create-a-topic"></a>Criar um tópico

Como um editor de um evento, você precisa criar um tópico de grade de eventos. O tópico refere-se a um ponto de extremidade para o qual os Publicadores podem enviar eventos.

1. Crie topic2.jscom o conteúdo a seguir. Consulte nossa [documentação de API](api.md) para obter detalhes sobre a carga.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Execute o comando a seguir para criar o tópico. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Execute o comando a seguir para verificar se o tópico foi criado com êxito. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Saída de exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Criar uma assinatura de evento

Os assinantes podem se registrar para eventos publicados em um tópico. Para receber qualquer evento, os assinantes precisarão criar uma assinatura de grade de eventos em um tópico de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscription2.jscom o conteúdo a seguir. Consulte nossa [documentação de API](api.md) para obter detalhes sobre a carga.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > O **ponto de extremidade** especifica que o assinante é um webhook.  O **endpointUrl** especifica a URL na qual o assinante está escutando eventos. Essa URL corresponde ao exemplo da função do Azure que você configurou anteriormente.
2. Execute o comando a seguir para criar a assinatura. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Execute o comando a seguir para verificar se a assinatura foi criada com êxito. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicar um evento

1. Crie event2.jscom o conteúdo a seguir. Consulte nossa [documentação de API](api.md) para obter detalhes sobre a carga.

    ```json
        [
          {
            "id": "eventId-func-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Execute o comando a seguir para publicar o evento

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificar a entrega de eventos

Você pode exibir o evento entregue no portal do Azure na opção **monitorar** da sua função.

## <a name="cleanup-resources"></a>Recursos de limpeza

* Execute o seguinte comando para excluir o tópico e todas as suas assinaturas

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Exclua a função do Azure criada no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um tópico de grade de eventos, uma assinatura e eventos publicados. Agora que você conhece as etapas básicas, consulte os seguintes artigos:

* Para solucionar problemas com o uso da grade de eventos do Azure no IoT Edge, consulte [Guia de solução de problemas](troubleshoot.md).
* Criar/atualizar assinatura com [filtros](advanced-filtering.md).
* Configurar a persistência do módulo de grade de eventos no [Linux](persist-state-linux.md) ou no [Windows](persist-state-windows.md)
* Siga a [documentação](configure-client-auth.md) para configurar a autenticação do cliente
* Encaminhar eventos para a grade de eventos do Azure na nuvem seguindo este [tutorial](forward-events-event-grid-cloud.md)
* [Monitorar tópicos e assinaturas na borda](monitor-topics-subscriptions.md)
---
title: Encaminhar eventos de borda para a grade de eventos Cloud-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Encaminhar eventos de borda para a nuvem da grade de eventos
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171594"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Tutorial: encaminhar eventos para a nuvem da grade de eventos

Este artigo percorre todas as etapas necessárias para encaminhar eventos de borda para a grade de eventos na nuvem do Azure. Você pode desejar fazer isso pelos seguintes motivos:

* Reagir a eventos de borda na nuvem.
* Encaminhar eventos para a grade de eventos na nuvem e use os hubs de eventos do Azure ou filas de armazenamento do Azure para eventos de buffer antes de processá-los na nuvem.

 Para concluir este tutorial, você precisa compreender os conceitos da grade de eventos no [Edge](concepts.md) e no [Azure](../concepts.md). Para obter tipos de destino adicionais, consulte [manipuladores de eventos](event-handlers.md). 

## <a name="prerequisites"></a>Pré-requisitos 
Para concluir este tutorial, você precisará de:

* **Assinatura do Azure** – crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Hub IOT do Azure e IOT Edge dispositivo** -siga as etapas no início rápido para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Criar tópico e assinatura da grade de eventos na nuvem

Crie um tópico e uma assinatura da grade de eventos na nuvem seguindo [este tutorial](../custom-event-quickstart-portal.md). Anote `topicURL` , `sasKey` e `topicName` do tópico recém-criado que você usará posteriormente no tutorial.

Por exemplo, se você criou um tópico chamado `testegcloudtopic` no oeste dos EUA, os valores teriam uma aparência semelhante a:

* **TopicUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **Topicname**: `testegcloudtopic`
* **SasKey**: disponível em **accessKey** do seu tópico. Use **key1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Criar tópico de grade de eventos na borda

1. Crie topic3.jscom o conteúdo a seguir. Consulte nossa [documentação de API](api.md) para obter detalhes sobre a carga.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Execute o comando a seguir para criar o tópico. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Execute o comando a seguir para verificar se o tópico foi criado com êxito. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Saída de exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Criar assinatura de grade de eventos na borda

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscription3.jscom o conteúdo a seguir. Consulte nossa [documentação de API](api.md) para obter detalhes sobre a carga.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > O **endpointUrl** especifica que a URL do tópico da grade de eventos na nuvem. O **sasKey** refere-se à chave do tópico da nuvem de grade de eventos. O valor em **topicname** será usado para carimbar todos os eventos de saída para a grade de eventos. Isso pode ser útil ao postar em um tópico de domínio da grade de eventos. Para obter mais informações sobre o tópico de domínio da grade de eventos, consulte [domínios de evento](../event-domains.md)

    Por exemplo,
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Execute o comando a seguir para criar a assinatura. O código de status HTTP de 200 OK deve ser retornado.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Execute o comando a seguir para verificar se a assinatura foi criada com êxito. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Publicar um evento na borda

1. Crie event3.jscom o conteúdo a seguir. Consulte a [documentação da API](api.md) para obter detalhes sobre a carga.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Execute o comando a seguir:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Verificar evento de borda na nuvem

Para obter informações sobre como exibir eventos entregues pelo tópico de nuvem, consulte o [tutorial](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Recursos de limpeza

* Execute o seguinte comando para excluir o tópico e todas as suas assinaturas

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Exclua o tópico e as assinaturas criadas na nuvem (a grade de eventos do Azure) também.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você publicou um evento na borda e encaminhado para a grade de eventos na nuvem do Azure. Agora que você conhece as etapas básicas para encaminhar para a grade de eventos na nuvem:

* Para solucionar problemas com o uso da grade de eventos do Azure no IoT Edge, consulte [Guia de solução de problemas](troubleshoot.md).
* Encaminhar eventos para IoTHub seguindo este [tutorial](forward-events-iothub.md)
* Encaminhar eventos para webhook na nuvem seguindo este [tutorial](pub-sub-events-webhook-cloud.md)
* [Monitorar tópicos e assinaturas na borda](monitor-topics-subscriptions.md)

---
title: Encaminhar eventos de grade de eventos para IoTHub-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Encaminhar eventos de grade de eventos para IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 36dc7d098892fb2be7c2ba3d75de7c7adef1a4f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171543"
---
# <a name="tutorial-forward-events-to-iothub"></a>Tutorial: encaminhar eventos para IoTHub

Este artigo percorre todas as etapas necessárias para encaminhar eventos de grade de eventos para outros módulos IoT Edge, IoTHub usando rotas. Você pode desejar fazer isso pelos seguintes motivos:

* Continue a usar qualquer investimento existente já instalado com o roteamento do edgeHub
* Preferir rotear todos os eventos de um dispositivo somente por meio do Hub IoT

Para concluir este tutorial, você precisa entender os seguintes conceitos:

- [Conceitos da grade de eventos](concepts.md)
- [Hub do IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Pré-requisitos 
Para concluir este tutorial, você precisará de:

* **Assinatura do Azure** – crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Hub IOT do Azure e IOT Edge dispositivo** -siga as etapas no início rápido para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Criar tópico

Como um editor de um evento, você precisa criar um tópico de grade de eventos. O tópico refere-se a um ponto de extremidade em que os editores podem enviar eventos para o.

1. Crie topic4.jscom o conteúdo a seguir. Consulte nossa [documentação de API](api.md) para obter detalhes sobre a carga.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Execute o comando a seguir para criar o tópico. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Execute o comando a seguir para verificar se o tópico foi criado com êxito. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Saída de exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Criar assinatura de evento

Os assinantes podem se registrar para eventos publicados em um tópico. Para receber qualquer evento, eles precisarão criar uma assinatura de grade de eventos em um tópico de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscription4.jscom o conteúdo abaixo. Consulte nossa [documentação de API](api.md) para obter detalhes sobre a carga.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > O `endpointType` especifica que o assinante é `edgeHub` . O `outputName` especifica a saída na qual o módulo de grade de eventos encaminhará eventos que correspondam a essa assinatura para edgeHub. Por exemplo, os eventos que correspondem à assinatura acima serão gravados `/messages/modules/eventgridmodule/outputs/sampleSub4` .
2. Execute o comando a seguir para criar a assinatura. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Execute o comando a seguir para verificar se a assinatura foi criada com êxito. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Configurar uma rota do Hub do Edge

Atualize a rota do Hub do Edge para encaminhar eventos da assinatura de evento a serem encaminhados para IoTHub da seguinte maneira:

1. Entre no [Portal do Azure](https://ms.portal.azure.com)
1. Navegue até o **Hub IOT**.
1. Selecione **IOT Edge** no menu
1. Selecione a ID do dispositivo de destino na lista de dispositivos.
1. Selecione **definir módulos**.
1. Selecione **Avançar** e para a seção rotas.
1. Nas rotas, adicione uma nova rota

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Por exemplo,

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > A rota acima encaminhará todos os eventos correspondidos para que essa assinatura seja encaminhada ao Hub IoT. Você pode usar os recursos de [Roteamento do Hub do Edge](../../iot-edge/module-composition.md) para filtrar ainda mais e rotear os eventos da grade de eventos para outros módulos IOT Edge.

## <a name="setup-iot-hub-route"></a>Configurar rota do Hub IoT

Consulte o [tutorial de roteamento do Hub IOT](../../iot-hub/tutorial-routing.md) para configurar uma rota do Hub IOT para que você possa exibir eventos encaminhados do módulo de grade de eventos. Use `true` para a consulta para manter o tutorial simples.  



## <a name="publish-an-event"></a>Publicar um evento

1. Crie event4.jscom o conteúdo a seguir. Consulte nossa [documentação de API](api.md) para obter detalhes sobre a carga.

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Execute o seguinte comando para publicar o evento:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificar a entrega de eventos

Consulte o tutorial de [Roteamento](../../iot-hub/tutorial-routing.md) do Hub IOT para ver as etapas para exibir os eventos.

## <a name="cleanup-resources"></a>Recursos de limpeza

* Execute o seguinte comando para excluir o tópico e todas as suas assinaturas na borda:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Exclua todos os recursos criados ao configurar o roteamento IoTHub na nuvem também.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um tópico de grade de eventos, assinatura de Hub de borda e eventos publicados. Agora que você conhece as etapas básicas para encaminhar para um Hub do Edge, consulte os seguintes artigos:

* Para solucionar problemas com o uso da grade de eventos do Azure no IoT Edge, consulte [Guia de solução de problemas](troubleshoot.md).
* Usar filtros de rota do [Hub do Edge](../../iot-edge/module-composition.md) para eventos de partição
* Configurar a persistência do módulo de grade de eventos no [Linux](persist-state-linux.md) ou no [Windows](persist-state-windows.md)
* Siga a [documentação](configure-client-auth.md) para configurar a autenticação do cliente
* Encaminhar eventos para a grade de eventos do Azure na nuvem seguindo este [tutorial](forward-events-event-grid-cloud.md)
* [Monitorar tópicos e assinaturas na borda](monitor-topics-subscriptions.md)
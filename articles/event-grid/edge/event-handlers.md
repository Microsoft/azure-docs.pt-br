---
title: Manipuladores de eventos e destinos – grade de eventos do Azure IoT Edge | Microsoft Docs
description: Manipuladores de eventos e destinos na grade de eventos no Edge
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171577"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Manipuladores de eventos e destinos na grade de eventos no Edge

Um manipulador de eventos é o local em que o evento é mais uma ação ou para processar o evento. Com o módulo grade de eventos no Edge, o manipulador de eventos pode estar no mesmo dispositivo de borda, em outro dispositivo ou na nuvem. Você pode usar qualquer webhook para manipular eventos ou enviar eventos para um dos manipuladores nativos, como a grade de eventos do Azure.

Este artigo fornece informações sobre como configurar cada um.

## <a name="webhook"></a>WebHook

Para publicar em um ponto de extremidade de webhook, defina `endpointType` como `WebHook` e forneça:

* endpointUrl: a URL do ponto de extremidade do webhook

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Grade de Eventos do Azure

Para publicar em um ponto de extremidade de nuvem da grade de eventos do Azure, defina `endpointType` como `eventGrid` e forneça:

* endpointUrl: URL do tópico da grade de eventos na nuvem
* sasKey: chave de SAS do tópico da grade de eventos
* topicname: nome para carimbar todos os eventos de saída para a grade de eventos. O nome do tópico é útil ao postar em um tópico de domínio da grade de eventos.

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

## <a name="iot-edge-hub"></a>Hub de IoT Edge

Para publicar em um módulo do Hub do Edge, defina `endpointType` como `edgeHub` e forneça:

* OutputType: a saída na qual o módulo de grade de eventos encaminhará eventos que correspondam a essa assinatura para edgeHub. Por exemplo, os eventos que correspondem à assinatura abaixo serão gravados em/messages/modules/eventgridmodule/outputs/sampleSub4.

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

## <a name="event-hubs"></a>Hubs de Eventos

Para publicar em um hub de eventos, defina `endpointType` como `eventHub` e forneça:

* connectionString: cadeia de conexão para o Hub de eventos específico que você pretende gerar por meio de uma política de acesso compartilhado.

    >[!NOTE]
    > A cadeia de conexão deve ser específica à entidade. Usar uma cadeia de conexão de namespace não funcionará. Você pode gerar uma cadeia de conexão específica da entidade navegando até o Hub de eventos específico no qual deseja publicar no portal do Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de caracteres connecection específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Filas de barramento de serviço

Para publicar em uma fila do barramento de serviço, defina `endpointType` como `serviceBusQueue` e forneça:

* connectionString: cadeia de conexão para a fila específica do barramento de serviço que você está direcionando gerada por meio de uma política de acesso compartilhado.

    >[!NOTE]
    > A cadeia de conexão deve ser específica à entidade. Usar uma cadeia de conexão de namespace não funcionará. Gere uma cadeia de conexão específica da entidade navegando até a fila específica do barramento de serviço na qual você gostaria de publicar no portal do Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de caracteres connecection específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Tópicos do Service Bus

Para publicar em um tópico do barramento de serviço, defina `endpointType` como `serviceBusTopic` e forneça:

* connectionString: cadeia de conexão para o tópico específico do barramento de serviço que você está direcionando gerado por uma política de acesso compartilhado.

    >[!NOTE]
    > A cadeia de conexão deve ser específica à entidade. Usar uma cadeia de conexão de namespace não funcionará. Gere uma cadeia de conexão específica da entidade navegando até o tópico específico do barramento de serviço que você deseja publicar no portal do Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova cadeia de caracteres connecection específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Filas de Armazenamento

Para publicar em uma fila de armazenamento, defina  `endpointType` como `storageQueue` e forneça:

* QueueName: o nome da fila de armazenamento na qual você está publicando.
* connectionString: cadeia de conexão para a conta de armazenamento na qual a fila de armazenamento está.

    >[!NOTE]
    > Hubs de eventos unline, filas do barramento de serviço e tópicos do barramento de serviço, a cadeia de conexão usada para filas de armazenamento não é específica à entidade. Em vez disso, ele deve, mas a cadeia de conexão para a conta de armazenamento.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```
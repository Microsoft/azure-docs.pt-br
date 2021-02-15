---
title: Publicar, assinar eventos localmente-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Publicar, assinar eventos localmente usando o webhook com a grade de eventos no IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2a7cc864366bd9a35c96dd453c0dc68f77d8abd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171441"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Tutorial: publicar, assinar eventos localmente

Este artigo orienta todas as etapas necessárias para publicar e assinar eventos usando a grade de eventos no IoT Edge.

> [!NOTE]
> Para saber mais sobre os tópicos e as assinaturas da grade de eventos do Azure, consulte [conceitos da grade de eventos](concepts.md).

## <a name="prerequisites"></a>Pré-requisitos 
Para concluir este tutorial, você precisará de:

* **Assinatura do Azure** – crie uma [conta gratuita](https://azure.microsoft.com/free) se você ainda não tiver uma. 
* **Hub IOT do Azure e IOT Edge dispositivo** -siga as etapas no guia de início rápido para dispositivos [Linux](../../iot-edge/quickstart-linux.md) ou [Windows](../../iot-edge/quickstart.md) se você ainda não tiver um.

## <a name="deploy-event-grid-iot-edge-module"></a>Implantar o módulo de IoT Edge da grade de eventos

Há várias maneiras de implantar módulos em um dispositivo IoT Edge e todos eles funcionam para a grade de eventos do Azure no IoT Edge. Este artigo descreve as etapas para implantar a grade de eventos em IoT Edge do portal do Azure.

>[!NOTE]
> Neste tutorial, você implantará o módulo de grade de eventos sem persistência. Isso significa que todos os tópicos e assinaturas criados neste tutorial serão excluídos quando você reimplantar o módulo. Para obter mais informações sobre como configurar a persistência, consulte os seguintes artigos: [persistir estado no Linux](persist-state-linux.md) ou [persistir o estado no Windows](persist-state-windows.md). Para cargas de trabalho de produção, recomendamos que você instale o módulo de grade de eventos com persistência.


### <a name="select-your-iot-edge-device"></a>Selecione seu dispositivo IoT Edge

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Navegue até seu Hub IoT.
1. Selecione **IOT Edge** no menu da seção **Gerenciamento de dispositivo automático** . 
1. Clique na ID do dispositivo de destino na lista de dispositivos
1. Selecione **definir módulos**. Mantenha a página aberta. Você continuará com as etapas na próxima seção.

### <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. O portal do Azure tem um assistente que o orienta na criação de um manifesto de implantação, em vez de criar o documento JSON manualmente.  Ele tem três etapas: **Adicionar módulos**, **Especificar rotas**, e **Rever implantação**.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar**
1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: eventgridmodule
   * **URI da imagem**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opções de Criação de Contêiner**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Clique em **Salvar**
 1. Continue na próxima seção para adicionar o módulo assinante da grade de eventos do Azure antes de implantá-los juntos.

    >[!IMPORTANT]
    > Neste tutorial, você implantará o módulo de grade de eventos com a autenticação de cliente desabilitada. Para cargas de trabalho de produção, recomendamos que você habilite a autenticação do cliente. Para obter mais informações sobre como configurar o módulo de grade de eventos com segurança, consulte [segurança e autenticação](security-authentication.md).
    > 
    > Se você estiver usando uma VM do Azure como um dispositivo de borda, adicione uma regra de porta de entrada para permitir o tráfego de entrada na porta 4438. Para obter instruções sobre como adicionar a regra, consulte [como abrir portas para uma VM](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Implantar o módulo IoT Edge do assinante de grade de eventos

Esta seção mostra como implantar outro módulo de IoT que atuaria como um manipulador de eventos para o qual os eventos podem ser entregues.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar** novamente. 
1. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
1. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: assinante
   * **URI da imagem**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opções de criação de contêiner**: nenhuma
1. Clique em **Salvar**
1. Clique em **Avançar** para continuar na seção rotas

 ### <a name="setup-routes"></a>Rotas de instalação

Mantenha as rotas padrão e selecione **Avançar** para continuar na seção revisão

### <a name="submit-the-deployment-request"></a>Enviar a solicitação de implantação

1. A seção revisão mostra o manifesto de implantação JSON criado com base em suas seleções na seção anterior. Confirme que você vê os dois módulos: **eventgridmodule** e **assinante** listados no JSON. 
1. Examine as informações da implantação e, em seguida, selecione **Enviar**. Depois de enviar a implantação, você retorna para a página do **dispositivo** .
1. Na **seção módulos**, verifique se os módulos **eventgrid** e **assinante** estão listados. E verifique se o **especificado em implantação** e **relatado por** colunas de dispositivo estão definidos como **Sim**.

    Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.

## <a name="create-a-topic"></a>Criar um tópico

Como um editor de um evento, você precisa criar um tópico de grade de eventos. Na grade de eventos do Azure, um tópico refere-se a um ponto de extremidade em que os editores podem enviar eventos.

1. Crie topic.jscom o conteúdo a seguir. Para obter detalhes sobre a carga, consulte nossa [documentação de API](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Execute o comando a seguir para criar um tópico de grade de eventos. Confirme que você vê o código de status HTTP `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Execute o comando a seguir para verificar se o tópico foi criado com êxito. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Saída de exemplo:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Criar uma assinatura de evento

Os assinantes podem se registrar para eventos publicados em um tópico. Para receber qualquer evento, você precisará criar uma assinatura de grade de eventos para um tópico de interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Crie subscription.jscom o conteúdo a seguir. Para obter detalhes sobre a carga, consulte nossa [documentação de API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > A propriedade **EndpointType** especifica que o assinante é um **webhook**.  O **endpointUrl** especifica a URL na qual o assinante está escutando eventos. Essa URL corresponde à amostra de assinante do Azure que você implantou anteriormente.
2. Execute o comando a seguir para criar uma assinatura para o tópico. Confirme que você vê o código de status HTTP `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Execute o comando a seguir para verificar se a assinatura foi criada com êxito. O código de status HTTP de 200 OK deve ser retornado.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicar um evento

1. Crie event.jscom o conteúdo a seguir. Para obter detalhes sobre a carga, consulte nossa [documentação de API](api.md).

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Execute o comando a seguir para publicar um evento.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificar a entrega de eventos

1. SSH ou RDP em sua VM IoT Edge.
1. Verifique os logs do Assinante:

    No Windows, execute o seguinte comando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   No Linux, execute o seguinte comando:

    ```sh
    sudo docker logs subscriber
    ```

    Saída de exemplo:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Recursos de limpeza

* Execute o comando a seguir para excluir o tópico e todas as suas assinaturas.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Exclua o módulo de assinante do seu dispositivo IoT Edge.


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um tópico de grade de eventos, uma assinatura e eventos publicados. Agora que você conhece as etapas básicas, consulte os seguintes artigos: 

- Para solucionar problemas com o uso da grade de eventos do Azure no IoT Edge, consulte [Guia de solução de problemas](troubleshoot.md).
- Criar/atualizar assinatura com [filtros](advanced-filtering.md).
- Habilitar a persistência do módulo de grade de eventos no [Linux](persist-state-linux.md) ou no [Windows](persist-state-windows.md)
- Siga a [documentação](configure-client-auth.md) para configurar a autenticação do cliente
- Encaminhar eventos para Azure Functions na nuvem seguindo este [tutorial](pub-sub-events-webhook-cloud.md)
- [Reagir a eventos de armazenamento de BLOBs em IoT Edge](react-blob-storage-events-locally.md)
- [Monitorar tópicos e assinaturas na borda](monitor-topics-subscriptions.md)


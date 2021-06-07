---
title: Reagir a eventos do módulo de armazenamento de BLOBs-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Reagir a eventos do módulo de armazenamento de BLOBs
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005054"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Tutorial: reagir a eventos de armazenamento de BLOBs em IoT Edge (versão prévia)
Este artigo mostra como implantar o armazenamento de BLOBs do Azure no módulo IoT, que atuaria como um editor de grade de eventos para enviar eventos sobre a criação de BLOB e exclusão de BLOB para a grade de eventos.  

Para obter uma visão geral do armazenamento de BLOBs do Azure no IoT Edge, consulte [armazenamento de BLOBs do Azure em IOT Edge](../../iot-edge/how-to-store-data-blob.md) e seus recursos.

> [!WARNING]
> O armazenamento de BLOBs do Azure na integração de IoT Edge com a grade de eventos está em versão prévia

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
   * **Opções de criação de contêiner**:

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
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
    > Neste tutorial, você aprenderá a implantar o módulo de grade de eventos para permitir solicitações HTTP/HTTPs, autenticação de cliente desabilitada. Para cargas de trabalho de produção, recomendamos que você habilite somente solicitações HTTPs e assinantes com autenticação de cliente habilitada. Para obter mais informações sobre como configurar o módulo de grade de eventos com segurança, consulte [segurança e autenticação](security-authentication.md).
    

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
1. Continue na próxima seção para adicionar o módulo de armazenamento de BLOBs do Azure

## <a name="deploy-azure-blob-storage-module"></a>Implantar o módulo de armazenamento de BLOBs do Azure

Esta seção mostra como implantar o módulo de armazenamento de BLOBs do Azure, que atuaria como um Publicador de grade de eventos que publica eventos de blob criados e excluídos.

### <a name="add-modules"></a>Adicionar módulos

1. Na seção **módulos de implantação** , selecione **Adicionar**
2. Nos tipos de módulos na lista suspensa, selecione **IOT Edge módulo**
3. Forneça as opções nome, imagem e contêiner criar do contêiner:

   * **Nome**: azureblobstorageoniotedge
   * **URI da imagem**: MCR.Microsoft.com/Azure-Blob-Storage:Latest
   * **Opções de criação de contêiner**:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - O módulo de armazenamento de BLOBs pode publicar eventos usando HTTPS e HTTP. 
   > - Se você habilitou a autenticação baseada em cliente para EventGrid, atualize o valor de EVENTGRID_ENDPOINT para permitir HTTPS, desta forma: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438` .
   > - Além disso, adicione outra variável `AllowUnknownCertificateAuthority=true` de ambiente ao JSON acima. Ao se comunicar com EventGrid por HTTPS, o **AllowUnknownCertificateAuthority** permite que o módulo de armazenamento confie em certificados de servidor EventGrid autoassinados.

4. Atualize o JSON que você copiou com as seguintes informações:

   - Substitua `<your storage account name>` por um nome de que você possa se lembrar. Os nomes de conta devem ter entre 3 e 24 caracteres, com letras minúsculas e números. Não são permitidos espaços.

   - Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

   - Substitua `<event grid module name>` pelo nome do seu módulo de grade de eventos.
   - Substitua `<storage mount>` de acordo com o sistema operacional do contêiner.
     - Para contêineres do Linux, **meu-volume:/blobroot**
     - Para contêineres do Windows,**meu-volume: C:/BlobRoot**

5. Clique em **Salvar**
6. Clique em **Avançar** para continuar na seção rotas

    > [!NOTE]
    > Se você estiver usando uma VM do Azure como o dispositivo de borda, adicione uma regra de porta de entrada para permitir o tráfego de entrada nas portas de host usadas neste tutorial: 4438, 5888, 8080 e 11002. Para obter instruções sobre como adicionar a regra, consulte [como abrir portas para uma VM](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Rotas de instalação

Mantenha as rotas padrão e selecione **Avançar** para continuar na seção revisão

### <a name="review-deployment"></a>Rever implantação

1. A seção revisão mostra o manifesto de implantação JSON criado com base em suas seleções na seção anterior. Confirme que você vê os quatro módulos a seguir: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **Subscriber** e **azureblobstorageoniotedge** que todos estão sendo implantados.
2. Examine as informações da implantação e, em seguida, selecione **Enviar**.

## <a name="verify-your-deployment"></a>Verificar sua implantação

1. Depois de enviar a implantação, você volta para a página do IoT Edge de seu hub IoT.
2. Selecione o **dispositivo de IOT Edge** de destino com a implantação para abrir seus detalhes.
3. Nos detalhes do dispositivo, verifique se os módulos eventgridmodule, subscriber e azureblobstorageoniotedge estão listados como ambos **especificados na implantação** e **relatados pelo dispositivo**.

   Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publicar eventos BlobCreated e BlobDeleted

1. Esse módulo cria automaticamente o tópico **MicrosoftStorage**. Verifique se ele existe
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Saída de exemplo:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver habilitada por meio da chave SAS, a chave SAS especificada anteriormente deverá ser adicionada como um cabeçalho. Portanto, a solicitação de ondulação será: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Para o fluxo HTTPS, se a autenticação do cliente estiver habilitada por meio do certificado, a solicitação de rotação será: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Os assinantes podem se registrar para eventos publicados em um tópico. Para receber qualquer evento, você precisará criar uma assinatura de grade de eventos para o tópico **MicrosoftStorage** .
    1. Crie blobsubscription.jscom o conteúdo a seguir. Para obter detalhes sobre a carga, consulte nossa [documentação de API](api.md)

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
       > A propriedade **EndpointType** especifica que o assinante é um **webhook**.  O **endpointUrl** especifica a URL na qual o assinante está escutando eventos. Essa URL corresponde ao exemplo de Azure function que você implantou anteriormente.

    2. Execute o comando a seguir para criar uma assinatura para o tópico. Confirme que você vê o código de status HTTP `200 OK` .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver habilitada por meio da chave SAS, a chave SAS especificada anteriormente deverá ser adicionada como um cabeçalho. Portanto, a solicitação de ondulação será: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver habilitada por meio do certificado, a solicitação de rotação será:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Execute o comando a seguir para verificar se a assinatura foi criada com êxito. O código de status HTTP de 200 OK deve ser retornado.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Saída de exemplo:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver habilitada por meio da chave SAS, a chave SAS especificada anteriormente deverá ser adicionada como um cabeçalho. Portanto, a solicitação de ondulação será: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Para o fluxo HTTPS, se a autenticação do cliente estiver habilitada por meio do certificado, a solicitação de rotação será: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Baixar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) e [conectá-lo ao armazenamento local](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Verificar a entrega de eventos

### <a name="verify-blobcreated-event-delivery"></a>Verificar a entrega de eventos do BlobCreated

1. Carregue arquivos como BLOBs de blocos no armazenamento local de Gerenciador de Armazenamento do Azure e o módulo publicará automaticamente os eventos de criação. 
2. Confira os logs do assinante para criar evento. Siga as etapas para [verificar a entrega de eventos](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída de exemplo:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Verificar a entrega de eventos do BlobDeleted

1. Exclua os BLOBs do armazenamento local usando Gerenciador de Armazenamento do Azure e o módulo publicará automaticamente os eventos de exclusão. 
2. Confira os logs do assinante para excluir evento. Siga as etapas para [verificar a entrega de eventos](pub-sub-events-webhook-local.md#verify-event-delivery)

    Saída de exemplo:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Parabéns! Você concluiu o tutorial. As seções a seguir fornecem detalhes sobre as propriedades do evento.

### <a name="event-properties"></a>Propriedades do evento

Aqui está a lista de propriedades de eventos com suporte e seus tipos e descrições. 

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos do armazenamento de blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grade de Eventos define o esquema de propriedades de nível superior. A Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que disparou o evento. Pode ser um dos seguintes valores: <ul><li>BlobCreated-os valores permitidos são: `PutBlob` e `PutBlockList`</li><li>BlobDeleted-os valores permitidos `DeleteBlob` são `DeleteAfterUpload` e `AutoDelete` . <p>O `DeleteAfterUpload` evento é gerado quando o blob é excluído automaticamente porque a propriedade desejada deleteAfterUpload está definida como true. </p><p>`AutoDelete` o evento é gerado quando o blob é excluído automaticamente porque o valor da propriedade desejada deleteAfterMinutes expirou.</p></li></ul>|
| clientRequestId | string | uma ID de solicitação fornecida pelo cliente para a operação da API de armazenamento. Essa ID pode ser usada para correlacionar os logs de diagnóstico do armazenamento do Azure usando o campo "Client-Request-ID" nos logs e pode ser fornecida em solicitações de cliente usando o cabeçalho "x-MS-Client-Request-ID". Para obter detalhes, consulte [formato de log](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID da solicitação gerada pelo serviço para a operação da API de armazenamento. Pode ser usada para correlacionar com os logs de diagnóstico do Armazenamento do Azure usando o campo "request-id-header" nos logs, e retornada pela inicialização da chamada á API no cabeçalho 'x-ms-request-id'. Consulte [Formato de Log](/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que você pode usar para executar operações condicionalmente. |
| contentType | string | O tipo de conteúdo especificado para o blob. |
| contentLength | inteiro | O tamanho do blob em bytes. |
| BlobType | string | O tipo de blob. Os valores válidos são "BlockBlob" ou "PageBlob". |
| url | string | O caminho para o blob. <br>Se o cliente usar uma API REST de BLOB, a URL terá essa estrutura: *\<storage-account-name\> . blob.Core.Windows.NET/ \<container-name\> / \<file-name\>*. <br>Se o cliente usar uma API REST Data Lake Storage, a URL terá essa estrutura: *\<storage-account-name\> . DFS.Core.Windows.NET/ \<file-system-name\> / \<file-name\>*. |


## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos da documentação do armazenamento de BLOBs:

- [Filtrar eventos de armazenamento de BLOBs](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Práticas recomendadas para consumo de eventos de armazenamento de BLOBs](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

Neste tutorial, você publicou eventos criando ou excluindo BLOBs em um armazenamento de BLOBs do Azure. Consulte os outros tutoriais para saber como encaminhar eventos para a nuvem (Hub de eventos do Azure ou Hub IoT do Azure): 

- [Encaminhar eventos para a Grade de Eventos do Azure](forward-events-event-grid-cloud.md)
- [Encaminhar eventos para o Hub IoT do Azure](forward-events-iothub.md)

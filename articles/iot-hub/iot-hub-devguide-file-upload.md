---
title: Entender o upload de arquivo do Hub IoT do Azure | Microsoft Docs
description: "Guia do desenvolvedor ‑ usar o recurso de upload de arquivo do Hub IoT para gerenciar o carregamento de arquivos de um dispositivo para um contêiner de Azure Storage Blob."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 75a6b9bc3ecfe6d6901bb38e312d62333f38daf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upload-files-with-iot-hub"></a>Carregar arquivos com o Hub IoT

Como detalhado no artigo [Pontos de extremidade do Hub IoT][lnk-endpoints], um dispositivo pode iniciar um upload de arquivo enviando uma notificação por meio de um ponto de extremidade voltado para o dispositivo (**/devices/{deviceId}/files**). Quando um dispositivo notifica o Hub IoT de um upload concluído, o Hub IoT envia uma mensagem de notificação de upload de arquivo por meio do ponto de extremidade voltado para o serviço **/messages/servicebound/filenotifications**.

Em vez da corretagem mensagens por meio do próprio Hub IoT, o Hub IoT age como um dispatcher para uma conta do Armazenamento do Azure associada. Um dispositivo solicita um token de armazenamento do Hub IoT específico para o arquivo que o dispositivo deseja carregar. O dispositivo usa o URI de SAS para carregar o arquivo de armazenamento e, quando o upload for concluído, o dispositivo enviará uma notificação de conclusão para o Hub IoT. O Hub IoT verifica se o upload do arquivo está concluído e então adiciona uma mensagem de notificação de upload de arquivo ao ponto de extremidade de notificação de arquivo voltado para o serviço.

Antes de carregar um arquivo no Hub IoT de um dispositivo, você deve configurar seu hub [associando uma conta do Armazenamento do Azure][lnk-associate-storage] a ele.

Em seguida, seu dispositivo pode [inicializar um carregamento][lnk-initialize] e [notificar o Hub IoT][lnk-notify] após a conclusão do carregamento. Opcionalmente, quando um dispositivo notifica o Hub IoT de que o carregamento foi concluído, o serviço pode gerar uma [mensagem de notificação][lnk-service-notification].

### <a name="when-to-use"></a>Quando usar

Use o carregamento de arquivos para enviar arquivos de mídia e lotes grandes de telemetria carregados por dispositivos conectados de forma intermitente ou compactados para economizar largura de banda.

Veja as[diretrizes de comunicação do dispositivo para a nuvem][lnk-d2c-guidance] se está em dúvida entre o uso de propriedades reportadas, mensagens do dispositivo para a nuvem ou carregamento do arquivo.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associar uma conta do Armazenamento do Azure com o Hub IoT

Para usar a funcionalidade de upload de arquivos, primeiro você deve vincular uma conta do Armazenamento do Azure para o Hub IoT. Você pode concluir essa tarefa por meio do [Portal do Azure][lnk-management-portal] ou programaticamente por meio das [APIs REST do provedor de recursos do Hub IoT][lnk-resource-provider-apis]. Depois de associar a uma conta do Armazenamento do Azure ao Hub IoT, o serviço retorna um URI de SAS para um dispositivo quando o dispositivo inicia uma solicitação de upload de arquivos.

> [!NOTE]
> Os [SDKs do Azure IoT][lnk-sdks] tratam automaticamente da recuperação do URI de SAS, do upload do arquivo e da notificação do Hub IoT de um upload concluído.


## <a name="initialize-a-file-upload"></a>Inicializar um upload de arquivo
O Hub IoT tem um ponto de extremidade especificamente para os dispositivos solicitarem um URI SAS para armazenamento a fim de carregar um arquivo. Para iniciar o processo de upload de arquivo, o dispositivo envia uma solicitação POST para `{iot hub}.azure-devices.net/devices/{deviceId}/files` com o seguinte corpo JSON:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

O Hub IoT retorna os seguintes dados, que são usados pelo dispositivo para carregar o arquivo:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Preterido: inicializar um carregamento de arquivo com um GET

> [!NOTE]
> Esta seção descreve a funcionalidade preterida de como receber um URI SAS do Hub IoT. Use o método POST descrito anteriormente.

O Hub IoT tem dois pontos de extremidade REST para dar suporte ao upload de arquivo, um para obter o URI de SAS para armazenamento e o outro para notificar o hub IoT de um upload concluído. O dispositivo inicia o processo de upload de arquivo, enviando um GET para o hub IoT em `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. O Hub IoT retorna:

* Um URI de SAS específico para o arquivo a ser carregado.
* Uma ID de correlação a ser usada quando o upload for concluído.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notificar o Hub IoT de um upload de arquivo concluído

O dispositivo é responsável por carregar o arquivo para o armazenamento usando os SDKs do Armazenamento do Azure. Após a conclusão do upload, o dispositivo envia uma solicitação POST para `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` com o seguinte corpo JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

O valor de `isSuccess` é um booliano que representa se o arquivo foi carregado com êxito. O código de status para `statusCode` é o status do carregamento do arquivo no armazenamento, e o `statusDescription` corresponde ao `statusCode`.

## <a name="reference-topics"></a>Tópicos de referência:

Os tópicos de referência a seguir fornecem a você mais informações sobre como carregar os arquivos de um dispositivo.

## <a name="file-upload-notifications"></a>Notificações de upload de arquivo

Opcionalmente, quando um dispositivo notifica o Hub IoT da conclusão de um upload, o Hub IoT pode gerar uma mensagem de notificação com o local de armazenamento e o nome do arquivo.

Como explicado em [Pontos de extremidade][lnk-endpoints], o Hub IoT fornece notificações de upload de arquivos por meio de um ponto de extremidade voltado para o serviço (**/messages/servicebound/fileuploadnotifications**) como mensagens. A semântica de recebimento das notificações de upload de arquivos é a mesma das mensagens da nuvem para o dispositivo e tem o mesmo [ciclo de vida da mensagem][lnk-lifecycle]. Cada mensagem recuperada do ponto de extremidade de notificação de upload de arquivos é um registro JSON com as seguintes propriedades:

| Propriedade | Descrição |
| --- | --- |
| EnqueuedTimeUtc |Carimbo de data/hora que indica quando a notificação foi criada. |
| deviceId |**DeviceId** do dispositivo que carregou o arquivo. |
| BlobUri |URI do arquivo carregado. |
| BlobName |Nome do arquivo carregado. |
| LastUpdatedTime |Carimbo de data/hora que indica quando o arquivo foi atualizado pela última vez. |
| BlobSizeInBytes |Tamanho do arquivo carregado. |

**Exemplo**. Este exemplo mostra o corpo de uma mensagem de notificação de carregamento de arquivo.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opções de configuração de notificação de upload de arquivo

Cada hub IoT expõe as seguintes opções de configuração para notificações de upload de arquivos:

| Propriedade | Descrição | Intervalo e padrão |
| --- | --- | --- |
| **enableFileUploadNotifications** |Controla se as notificações de upload de arquivos serão gravadas no ponto de extremidade de notificações de arquivo. |Bool. Padrão: True. |
| **fileNotifications.ttlAsIso8601** |TTL padrão para notificações de upload de arquivos. |Intervalo ISO_8601 de até 48H (mínimo de um minuto). Padrão: 1 hora. |
| **fileNotifications.lockDuration** |Duração de bloqueio para a fila de notificações de upload de arquivos. |5 a 300 segundos (mínimo de cinco segundos). Padrão: 60 segundos. |
| **fileNotifications.maxDeliveryCount** |Contagem máxima de entregas para a fila de notificação de upload de arquivos. |1 a 100. Padrão: 100. |

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT][lnk-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
* [Limitação e cotas][lnk-quotas] descreve as cotas e os comportamentos de limitação que se aplicam ao serviço Hub IoT.
* [SDKs de dispositivo e serviço IoT do Azure][lnk-sdks] lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.
* A [linguagem de consulta do Hub IoT][lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do Hub IoT sobre dispositivos gêmeos e trabalhos.
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt] fornece mais informações sobre o suporte do Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a carregar arquivos de dispositivos usando o Hub IoT, talvez se interesse pelos tópicos a seguir do Guia do desenvolvedor do Hub IoT:

* [Gerenciar identidades do dispositivo no Hub IoT][lnk-devguide-identities]
* [Controlar o acesso ao Hub IoT][lnk-devguide-security]
* [Usar dispositivos gêmeos para sincronizar o estado e as configurações][lnk-devguide-device-twins]
* [Invocar um método direto em um dispositivo][lnk-devguide-directmethods]
* [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelo seguinte tutorial de Hub IoT:

* [Como carregar arquivos de dispositivos para a nuvem com o Hub IoT][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

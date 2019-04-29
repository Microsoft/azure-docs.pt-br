---
title: Entender o upload de arquivo do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ usar o recurso de upload de arquivo do Hub IoT para gerenciar o carregamento de arquivos de um dispositivo para um contêiner de Azure Storage Blob.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 217d348eacab30b90e06fe805d9cdb0cf32349ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60950373"
---
# <a name="upload-files-with-iot-hub"></a>Carregar arquivos com o Hub IoT

Como detalhado no artigo [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md), um dispositivo pode iniciar um upload de arquivo enviando uma notificação por meio de um ponto de extremidade voltado para o dispositivo (**/devices/{deviceId}/files**). Quando um dispositivo notifica o Hub IoT de um upload concluído, o Hub IoT envia uma mensagem de notificação de upload de arquivo por meio do ponto de extremidade voltado para o serviço **/messages/servicebound/filenotifications**.

Em vez da corretagem mensagens por meio do próprio Hub IoT, o Hub IoT age como um dispatcher para uma conta do Armazenamento do Azure associada. Um dispositivo solicita um token de armazenamento do Hub IoT específico para o arquivo que o dispositivo deseja carregar. O dispositivo usa o URI de SAS para carregar o arquivo de armazenamento e, quando o upload for concluído, o dispositivo enviará uma notificação de conclusão para o Hub IoT. O Hub IoT verifica se o upload do arquivo está concluído e então adiciona uma mensagem de notificação de upload de arquivo ao ponto de extremidade de notificação de arquivo voltado para o serviço.

Antes de carregar um arquivo no Hub IoT de um dispositivo, você deve configurar seu hub [associando uma conta do Armazenamento do Azure](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) a ele.

Em seguida, seu dispositivo pode [inicializar um upload](iot-hub-devguide-file-upload.md#initialize-a-file-upload) e [notificar o Hub IoT](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) após a conclusão do upload. Opcionalmente, quando um dispositivo notifica o Hub IoT de que o upload foi concluído, o serviço pode gerar uma [mensagem de notificação](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>Quando usar

Use o carregamento de arquivos para enviar arquivos de mídia e lotes grandes de telemetria carregados por dispositivos conectados de forma intermitente ou compactados para economizar largura de banda.

Veja as [Diretrizes de comunicação do dispositivo para nuvem](iot-hub-devguide-d2c-guidance.md) se está em dúvida entre o uso de propriedades reportadas, mensagens do dispositivo para nuvem ou upload do arquivo.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associar uma conta do Armazenamento do Azure com o Hub IoT

Para usar a funcionalidade de upload de arquivos, primeiro você deve vincular uma conta do Armazenamento do Azure para o Hub IoT. Você pode concluir essa tarefa por meio do portal do Azure ou programaticamente por meio das [APIs REST do provedor de recursos do Hub IoT](/rest/api/iothub/iothubresource). Depois de associar uma conta do Armazenamento do Azure ao Hub IoT, o serviço retorna um URI de SAS para um dispositivo quando o dispositivo iniciar uma solicitação de upload de arquivos.

Os [arquivos de upload do seu dispositivo para a nuvem com os ](iot-hub-csharp-csharp-file-upload.md)guias de instruções do Hub IoT fornecem uma explicação completa do processo de upload de arquivo. Esses guias de instruções mostram como usar o portal do Azure para associar uma conta de armazenamento a um hub IoT.

> [!NOTE]
> Os [SDKs do Azure IoT](iot-hub-devguide-sdks.md) tratam automaticamente da recuperação do URI de SAS, do upload do arquivo e da notificação do Hub IoT de um upload concluído.

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
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Preterido: inicializar um carregamento de arquivo com um GET

> [!NOTE]
> Esta seção descreve a funcionalidade preterida de como receber um URI SAS do Hub IoT. Use o método POST descrito anteriormente.

O Hub IoT tem dois pontos de extremidade REST para dar suporte ao upload de arquivo, um para obter o URI de SAS para armazenamento e o outro para notificar o hub IoT de um upload concluído. O dispositivo inicia o processo de upload de arquivo enviando um GET para o hub IoT em `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. O Hub IoT retorna:

* Um URI de SAS específico para o arquivo a ser carregado.

* Uma ID de correlação a ser usada quando o upload for concluído.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notificar o Hub IoT de um upload de arquivo concluído

O dispositivo carrega o arquivo para o armazenamento usando os SDKs de Armazenamento do Azure. Após a conclusão do upload, o dispositivo envia uma solicitação POST para `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` com o seguinte corpo JSON:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

O valor de `isSuccess` é um tipo booliano que representa se o arquivo foi carregado com êxito. O código de status para `statusCode` é o status do carregamento do arquivo no armazenamento, e o `statusDescription` corresponde ao `statusCode`.

## <a name="reference-topics"></a>Tópicos de referência:

Os tópicos de referência a seguir fornecem a você mais informações sobre como carregar os arquivos de um dispositivo.

## <a name="file-upload-notifications"></a>Notificações de upload de arquivo

Opcionalmente, quando um dispositivo notifica o Hub IoT de que o upload foi concluído, o Hub IoT gera uma mensagem de notificação. Essa mensagem contém o nome e o local de armazenamento do arquivo.

Como explicado em [Pontos de extremidade](iot-hub-devguide-endpoints.md), o Hub IoT fornece notificações de upload de arquivos por meio de um ponto de extremidade voltado para o serviço (**/messages/servicebound/fileuploadnotifications**) como mensagens. A semântica de recebimento das notificações de upload de arquivos é a mesma das mensagens da nuvem para dispositivo e tem o mesmo [ciclo de vida da mensagem](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle). Cada mensagem recuperada do ponto de extremidade de notificação de upload de arquivos é um registro JSON com as seguintes propriedades:

| Propriedade | DESCRIÇÃO |
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

Cada hub IoT tem as seguintes opções de configuração para notificações de upload de arquivos:

| Propriedade | DESCRIÇÃO | Intervalo e padrão |
| --- | --- | --- |
| **enableFileUploadNotifications** |Controla se as notificações de upload de arquivos serão gravadas no ponto de extremidade de notificações de arquivo. |Bool. Padrão: True. |
| **fileNotifications.ttlAsIso8601** |TTL padrão para notificações de upload de arquivos. |Intervalo ISO_8601 de até 48H (mínimo de um minuto). Padrão: 1 hora. |
| **fileNotifications.lockDuration** |Duração de bloqueio para a fila de notificações de upload de arquivos. |5 a 300 segundos (mínimo de cinco segundos). Padrão: 60 segundos. |
| **fileNotifications.maxDeliveryCount** |Contagem máxima de entregas para a fila de notificação de upload de arquivos. |1 a 100. Padrão: 100. |

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md) descrevem os vários pontos de extremidade do hub IoT para as operações de tempo de execução e de gerenciamento.

* [Limitação e cotas](iot-hub-devguide-quotas-throttling.md) descreve as cotas e os comportamentos de limitação que se aplicam ao serviço do Hub IoT.

* [SDKs do dispositivo e do serviço IoT do Azure](iot-hub-devguide-sdks.md) lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.

* [Linguagem de consulta do Hub IoT](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para recuperar informações do Hub IoT sobre dispositivos gêmeos e trabalhos.

* O [suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do Hub IoT ao protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a carregar arquivos de dispositivos usando o Hub IoT, talvez se interesse pelos tópicos a seguir do Guia do desenvolvedor do Hub IoT:

* [Gerenciar identidades do dispositivo no Hub IoT](iot-hub-devguide-identity-registry.md)

* [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)

* [Usar dispositivos gêmeos para sincronizar o estado e as configurações](iot-hub-devguide-device-twins.md)

* [Invocar um método direto em um dispositivo](iot-hub-devguide-direct-methods.md)

* [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md)

Para experimentar alguns dos conceitos descritos neste artigo, consulte o tutorial do Hub IoT a seguir:

* [Como carregar arquivos de dispositivos para a nuvem com o Hub IoT](iot-hub-csharp-csharp-file-upload.md)
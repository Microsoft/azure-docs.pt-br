---
title: Usar modelos de Plug and Play de IoT em uma solução | Microsoft Docs
description: Como um construtor de soluções, saiba como você pode usar os modelos de Plug and Play de IoT em sua solução de IoT.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7e0d93d3fd179d672b49967c575bbfb9c0fc5de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213684"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>Usar modelos de Plug and Play de IoT em uma solução de IoT

Este artigo descreve como, em uma solução de IoT, você pode identificar a ID do modelo de um dispositivo de Plug and Play de IoT e, em seguida, recuperar sua definição de modelo.

Há duas categorias amplas de uma solução de IoT:

- Uma *solução criada por finalidade* funciona com um conjunto conhecido de modelos para os dispositivos IOT plug and Play que se conectarão à solução. Você usa esses modelos ao desenvolver a solução.

- Uma solução *controlada por modelos* pode funcionar com o modelo de qualquer dispositivo de plug and Play de IOT. A criação de uma solução orientada por modelos é mais complexa, mas o benefício é que sua solução funciona com qualquer dispositivo que possa ser adicionado no futuro. Uma solução IoT controlada por modelos recupera um modelo e usa-o para determinar a telemetria, as propriedades e os comandos que o dispositivo implementa.

Para usar um modelo de Plug and Play IoT, uma solução de IoT:

1. Identifica a ID do modelo implementada pelo dispositivo Plug and Play de IoT, módulo ou módulo IoT Edge conectado à solução.

1. Usa a ID do modelo para recuperar a definição do modelo do dispositivo conectado de um repositório de modelos ou de um repositório personalizado.

## <a name="identify-model-id"></a>Identificar ID do modelo

Quando um dispositivo de Plug and Play IoT se conecta ao Hub IoT, ele registra a ID do modelo que ele implementa com o Hub IoT.

O Hub IoT notifica a solução com a ID do modelo do dispositivo como parte do fluxo de conexão do dispositivo.

Uma solução pode obter a ID do modelo do dispositivo IoT Plug and Play usando um dos três métodos a seguir:

### <a name="get-device-twin-api"></a>Obter API de dispositivo

A solução pode usar a API de [obtenção de dispositivo](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) para recuperar a ID de modelo do dispositivo de plug and Play de IOT.

> [!TIP]
> Para módulos e módulos de IoT Edge, use [ModuleClient. Getentrelaçar](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin).

No trecho de resposta do dispositivo de dispositivos a seguir, `modelId` contém a ID do modelo de um dispositivo de plug and Play IOT:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

### <a name="get-digital-twin-api"></a>Obter a API de entrelaça digital

A solução pode usar a API de [obtenção digital](/rest/api/iothub/service/digitaltwin/getdigitaltwin) para recuperar a ID do modelo implementado pelo dispositivo de plug and Play de IOT.

No seguinte trecho de resposta digital de FileUp, `$metadata.$model` contém a ID do modelo de um dispositivo de plug and Play IOT:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="digital-twin-change-event-notification"></a>Notificação de evento de alteração de troca digital

Uma conexão de dispositivo resulta em uma notificação de [evento de alteração de troca digital](concepts-digital-twin.md#digital-twin-change-events) . Uma solução precisa assinar essa notificação de evento. Para saber como habilitar o roteamento para eventos de computação digital, consulte [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

A solução pode usar o evento mostrado no trecho a seguir para saber mais sobre o dispositivo IoT Plug and Play que está se conectando e obter sua ID de modelo:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

## <a name="retrieve-a-model-definition"></a>Recuperar uma definição de modelo

Uma solução usa a ID de modelo identificada acima para recuperar a definição de modelo correspondente.

Uma solução pode obter a definição do modelo usando uma das seguintes opções:

### <a name="model-repository"></a>Repositório de modelos

As soluções podem usar o [repositório](concepts-model-repository.md) de modelos para recuperar modelos. Os criadores de dispositivos ou os criadores de solução devem carregar seus modelos no repositório com antecedência para que a solução possa recuperá-los.

Depois de identificar a ID do modelo para uma nova conexão de dispositivo, siga estas etapas:

1. Recupere a definição do modelo usando a ID do modelo do repositório de modelos. Para obter mais informações, consulte [repositório de modelos de dispositivo](concepts-model-repository.md).

1. Usando a definição de modelo do dispositivo conectado, você pode enumerar os recursos do dispositivo.

1. Usando os recursos enumerados do dispositivo, você pode permitir que os usuários [interajam com o dispositivo](quickstart-service.md).

### <a name="custom-store"></a>Repositório personalizado

As soluções podem armazenar essas definições de modelo em um sistema de arquivos local, em um repositório de arquivos público ou usar uma implementação personalizada.

Depois de identificar a ID do modelo para uma nova conexão de dispositivo, siga estas etapas:

1. Recupere a definição do modelo usando a ID do modelo do seu repositório personalizado.

1. Usando a definição de modelo do dispositivo conectado, você pode enumerar os recursos do dispositivo. 

1. Usando os recursos enumerados do dispositivo, você pode permitir que os usuários [interajam com o dispositivo](quickstart-service.md).  

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a integrar os modelos de IoT Plug and Play em uma solução de IoT, algumas próximas etapas sugeridas são:

- [Interagir com um dispositivo da sua solução](quickstart-service.md)
- [API REST de monodigital de IoT](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)

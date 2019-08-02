---
title: Entender o formato de mensagem do Hub IoT do Azure| Microsoft Docs
description: Guia do desenvolvedor - descreve o formato e o conteúdo esperado de mensagens do IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: e2aafa195fa463a405e2132cd41fada8d6903961
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450074"
---
# <a name="create-and-read-iot-hub-messages"></a>Criar e ler mensagens do Hub IoT

Para suportar a interoperabilidade contínua entre protocolos, o Hub IoT define um formato de mensagem comum para todos os protocolos voltados para o dispositivo. Esse formato de mensagem é usado para mensagens de [roteamento de dispositivo para nuvem](iot-hub-devguide-messages-d2c.md) e de [nuvem para dispositivo](iot-hub-devguide-messages-c2d.md). 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O Hub IoT implementa mensagens de dispositivo para nuvem usando um padrão de sistema de mensagens de streaming. As mensagens de dispositivo para nuvem do Hub IoT são mais semelhantes a *eventos* de [Hubs de Eventos](/azure/event-hubs/) do que a *mensagens* do [Barramento de Serviço](/azure/service-bus-messaging/) no sentido de que há um volume alto de eventos passando pelo serviço que podem ser lidos por vários leitores.

Uma mensagem do Hub IoT consiste em:

* Um conjunto predeterminado de *propriedades do sistema*, conforme listado abaixo.

* Um conjunto de *propriedades do aplicativo*. Um dicionário de propriedades de cadeia de caracteres que o aplicativo pode definir e acessar sem precisar desserializar o corpo da mensagem. O Hub IoT nunca modifica essas propriedades.

* Um corpo de binário opaco.

Os valores e nomes de propriedade podem conter apenas caracteres alfanuméricos ASCII mais ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` quando você envia mensagens de dispositivo para nuvem usando o protocolo HTTPS ou envia mensagens da nuvem para dispositivo.

As mensagens de dispositivo para a nuvem com o Hub IoT têm as seguintes características:

* As mensagens do dispositivo para a nuvem são duráveis e mantidas em um ponto de extremidade de **mensagens/eventos** padrão do Hub IoT por até sete dias.

* As mensagens de dispositivo para a nuvem podem ter no máximo 256 KB e podem ser agrupadas em lotes para otimizar os envios. Os lotes podem ter no máximo 256 KB.

* O Hub IoT não permite o particionamento arbitrário. As mensagens do dispositivo para a nuvem são particionadas com base em sua **deviceId**de origem.

* Conforme explicado em [Controlar o acesso ao IoT Hub](iot-hub-devguide-security.md), Hub IoT permite a autenticação por dispositivo e controle de acesso.

* Você pode gravar mensagens com informações que vão para as propriedades do aplicativo. Para obter mais informações, consulte [aprimoramentos de mensagem](iot-hub-message-enrichments-overview.md).

Para obter mais informações de como codificar e decodificar mensagens enviadas usando diferentes protocolos, confira [SDKs de IoT do Azure](iot-hub-devguide-sdks.md).

A tabela a seguir lista o conjunto de propriedades do sistema em mensagens do Hub IoT.

| Propriedade | DESCRIÇÃO | O usuário é configurável? |
| --- | --- | --- |
| message-id |Um identificador configurável pelo usuário para a mensagem utilizada para padrões de resposta à solicitação. Formato: Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Sim |
| número de sequência |Um número (exclusivo por fila de dispositivos) atribuído pelo Hub IoT a cada mensagem da nuvem para o dispositivo. | Não para mensagens C2D, caso contrário, sim. |
| para |Um destino especificado em mensagens [Da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md) . | Não para mensagens C2D, caso contrário, sim. |
| absolute-expiry-time |Data e hora de expiração da mensagem. | Sim |
| iothub-enqueuedtime |Data e hora a [dispositivo para nuvem](iot-hub-devguide-d2c-guidance.md) mensagem foi recebida pelo IoT Hub. | Não para mensagens D2C, caso contrário, sim. |
| correlation-id |Uma cadeia de propriedade em uma mensagem de resposta que geralmente contém a MessageId da solicitação em padrões de solicitação-resposta. | Sim |
| user-id |Uma ID usada para especificar a origem das mensagens. Quando as mensagens são geradas pelo Hub IoT, são definidas como `{iot hub name}`. | Não |
| iothub-ack |Um gerador de mensagem de comentários. Essa propriedade é usada em mensagens da nuvem para o dispositivo para solicitar ao Hub IoT a geração de mensagens de comentários como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: **nenhum** (padrão): nenhuma mensagem de comentários é gerada, **positivo**: ocorrerá o recebimento de uma mensagem de comentários se a mensagem estiver completa, **negativo**: ocorrerá o recebimento de uma mensagem de comentários se a mensagem expirar (ou se a contagem máxima de entrega tiver sido atingida) sem ser concluída pelo dispositivo ou **total**: positivos e negativos. <!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | Sim |
| iothub-connection-device-id |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **deviceId** do dispositivo que enviou a mensagem. | Não para mensagens D2C, caso contrário, sim. |
| iothub-connection-auth-generation-id |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **generationId** (de acordo com as [Propriedades de identidade de dispositivo](iot-hub-devguide-identity-registry.md#device-identity-properties)) do dispositivo que enviou a mensagem. | Não para mensagens D2C, caso contrário, sim. |
| iothub-connection-auth-method |Um método de autenticação definido pelo Hub IoT em mensagens do dispositivo para a nuvem. Essa propriedade contém informações sobre o método de autenticação usado para autenticar o dispositivo que envia a mensagem. <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | Não para mensagens D2C, caso contrário, sim. |
| iothub-creation-time-utc | Data e hora em que a mensagem foi criada em um dispositivo. Um dispositivo deve definir esse valor explicitamente. | Sim |

## <a name="message-size"></a>Tamanho da mensagem

O Hub IoT mede o tamanho da mensagem independentemente do protocolo, considerando apenas a carga real. O tamanho em bytes é calculado como a soma do seguinte:

* O tamanho do corpo em bytes.
* O tamanho em bytes de todos os valores das propriedades do sistema de mensagens.
* O tamanho em bytes de todos os nomes e valores de propriedade do usuário.

Valores e nomes de propriedade são limitados a caracteres ASCII, portanto, o comprimento das cadeias de caracteres é igual ao tamanho em bytes.

## <a name="anti-spoofing-properties"></a>Propriedades antifalsificação

Para evitar a falsificação em mensagens do dispositivo para a nuvem, o Hub IoT carimba todas as mensagens com as seguintes propriedades:

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

As duas primeiras contêm a **deviceId** e a **generationId** do dispositivo de origem, conforme as [Propriedades de identidade do dispositivo](iot-hub-devguide-identity-registry.md#device-identity-properties).

A propriedade **iothub-connection-auth-method** contém um objeto JSON serializado com as seguintes propriedades:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre limites de tamanho de mensagem no Hub IoT, confira [Cotas e limitação do Hub IoT](iot-hub-devguide-quotas-throttling.md).

* Para saber como criar e ler mensagens do Hub IoT em várias linguagens de programação, confira os [Inícios Rápidos](quickstart-send-telemetry-node.md).

---
title: Entender mensagens de nuvem para o dispositivo Hub IoT do Azure| Microsoft Docs
description: Este guia do desenvolvedor discute como usar mensagens da nuvem para o dispositivo com o Hub IoT. Ele inclui informações sobre o ciclo de vida da mensagem e as opções de configuração.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: b0057815bee46d6708886302ff5b598c89b47e8f
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335739"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Enviar mensagens da nuvem para o dispositivo de um hub IoT

Para enviar notificações unidirecionais para um aplicativo de dispositivo do back-end da solução, envie mensagens da nuvem para o dispositivo do Hub IoT para seu dispositivo. Para obter uma discussão sobre outras opções de nuvem para dispositivo com suporte pelo Hub IoT do Azure, consulte [diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Você envia mensagens da nuvem para o dispositivo por meio de um ponto de extremidade voltado para o serviço, */messages/devicebound*. Em seguida, um dispositivo recebe as mensagens por meio de um ponto de extremidade específico do dispositivo, */Devices/{deviceId}/messages/devicebound*.

Para direcionar cada mensagem da nuvem para o dispositivo em um único dispositivo, o Hub IoT define a propriedade **to** como */Devices/{deviceId}/messages/devicebound*.

Cada fila de dispositivo contém, no máximo, 50 mensagens da nuvem para o dispositivo. Para tentar enviar mais mensagens para o mesmo dispositivo resulta em um erro.

## <a name="the-cloud-to-device-message-life-cycle"></a>O ciclo de vida da mensagem da nuvem para o dispositivo

Para garantir a entrega de mensagens pelo menos uma vez, o Hub IoT persiste as mensagens da nuvem para o dispositivo em filas por dispositivo. Para que o Hub IoT remova as mensagens da fila, os dispositivos devem confirmar explicitamente a *conclusão*. Essa abordagem garante a resiliência contra conectividade e falhas de dispositivo.

O gráfico de estado do ciclo de vida é exibido no diagrama a seguir:

![Ciclo de vida da mensagem da nuvem para o dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando o serviço do Hub IoT envia uma mensagem para um dispositivo, o serviço define o estado da mensagem como enfileirada. Quando um dispositivo quiser *receber* uma mensagem, o Hub IOT *bloqueará* a mensagem definindo o estado como *invisível*. Esse estado permite que outros threads no dispositivo comecem a receber outras mensagens. Quando um thread de dispositivo conclui o processamento de uma mensagem, ele notifica o Hub IoT concluindo  a mensagem. Em seguida, o Hub IoT define o estado como *concluído*.

Um dispositivo também pode:

* *Rejeite* a mensagem, o que faz com que o Hub IOT a defina para o estado de *mensagens mortas* . Os dispositivos que se conectam por meio do protocolo MQTT (transporte de telemetria de enfileiramento de mensagens) não podem rejeitar mensagens da nuvem para o dispositivo.

* *Abandone* a mensagem, o que faz com que o Hub IOT Coloque a mensagem de volta na fila, com o estado definido como enfileirado. Os dispositivos que se conectam pelo protocolo MQTT não podem abandonar mensagens da nuvem para o dispositivo.

Um thread pode falhar ao processar uma mensagem sem notificar o Hub IoT. Nesse caso, as mensagens são automaticamente transferidas do estado *invisível* de volta para o estado enfileirado após um tempo limite de *visibilidade* (ou tempo limite de *bloqueio* ). O valor desse tempo-limite é de um minuto e não pode ser alterado.

A propriedade **contagem máxima de entrega** no Hub IOT determina o número máximo de vezes que uma mensagem pode fazer a transição entre os Estados enfileirados e  invisíveis. Depois desse número de transições, o Hub IoT define o estado da mensagem para *mensagens mortas*. Da mesma forma, o Hub IoT define o estado de uma mensagem para *mensagens mortas* após seu tempo de expiração. Para obter mais informações, consulte [vida útil](#message-expiration-time-to-live).

O artigo [como enviar mensagens da nuvem para o dispositivo com o Hub IOT](iot-hub-csharp-csharp-c2d.md) mostra como enviar mensagens da nuvem para o dispositivo da nuvem e recebê-las em um dispositivo.

Um dispositivo normalmente conclui uma mensagem da nuvem para o dispositivo quando a perda da mensagem não afeta a lógica do aplicativo. Um exemplo disso pode ser quando o dispositivo persistiu o conteúdo da mensagem localmente ou executou uma operação com êxito. A mensagem também pode transportar informações transitórias, cuja perda não afetaria a funcionalidade do aplicativo. Às vezes, para tarefas de execução longa, você pode:

* Conclua a mensagem da nuvem para o dispositivo depois que o dispositivo persistir a descrição da tarefa no armazenamento local.

* Notificar o back-end da solução com uma ou mais mensagens de dispositivo para a nuvem em vários estágios de progresso da tarefa.

## <a name="message-expiration-time-to-live"></a>Expiração da mensagem (vida útil)

Todas as mensagens da nuvem para o dispositivo têm um tempo de expiração. Esse tempo é definido por um dos seguintes:

* A propriedade **ExpiryTimeUtc** no serviço
* O Hub IoT, usando a *vida* útil padrão especificada como uma propriedade de Hub IOT

Consulte [Opções de configuração da nuvem para o dispositivo](#cloud-to-device-configuration-options).

Uma maneira comum de aproveitar uma expiração de mensagem e evitar o envio de mensagens para dispositivos desconectados é definir valores curtos *de vida* útil. Essa abordagem alcança o mesmo resultado que manter o estado de conexão do dispositivo, mas é mais eficiente. Quando você solicita confirmações de mensagem, o Hub IoT notifica quais dispositivos são:

* São capazes de receber mensagens.
* Não estão online ou falharam.

## <a name="message-feedback"></a>Comentários da mensagem

Quando você envia uma mensagem da nuvem para o dispositivo, o serviço pode solicitar a entrega de comentários por mensagem sobre o estado final dessa mensagem. Você faz isso definindo a propriedade de aplicativo **iothub-ACK** na mensagem da nuvem para o dispositivo que está sendo enviada para um dos quatro valores a seguir:

| Valor da propriedade ACK | Comportamento |
| ------------ | -------- |
| nenhum     | O Hub IoT não gera uma mensagem de comentários (comportamento padrão). |
| positivo | Se a mensagem da nuvem para o dispositivo atingir o estado *concluído* , o Hub IOT gerará uma mensagem de comentários. |
| seriamente | Se a mensagem da nuvem para o dispositivo atingir o  estado inativo, o Hub IOT gerará uma mensagem de comentários. |
| completo     | O Hub IoT gera uma mensagem de comentários em ambos os casos. |

Se o valor de **ACK** estiver *cheio*e você não receber uma mensagem de comentários, isso significa que a mensagem de comentários expirou. O serviço não pode saber o que aconteceu com a mensagem original. Na prática, um serviço deve garantir que possa processar os comentários antes que eles expirem. O tempo de expiração máximo é de dois dias, o que deixa o tempo para que o serviço seja executado novamente se ocorrer uma falha.

Conforme explicado em [pontos](iot-hub-devguide-endpoints.md)de extremidade, o Hub IOT entrega comentários por meio de um ponto de extremidades voltado para o serviço, */messages/servicebound/feedback*, como mensagens. A semântica de recebimento dos comentários é a mesma das mensagens da nuvem para o dispositivo. Sempre que possível, os comentários de mensagem são feitos em lotes em uma única mensagem, com o seguinte formato:

| Propriedade     | Descrição |
| ------------ | ----------- |
| EnqueuedTime | Um carimbo de data/hora que indica quando a mensagem de comentário foi recebida pelo Hub |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma matriz de registros serializada em JSON, cada um com as seguintes propriedades:

| Propriedade           | Descrição |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Um carimbo de data/hora que indica quando o resultado da mensagem ocorreu (por exemplo, o Hub recebeu a mensagem de comentário ou a mensagem original expirou) |
| OriginalMessageId  | A *MessageId* da mensagem da nuvem para o dispositivo à qual essas informações de comentários se relacionam |
| StatusCode         | Uma cadeia de caracteres necessária, usada em mensagens de comentários que são geradas pelo Hub IoT: <br/> *Êxito* <br/> *Vencer* <br/> *DeliveryCountExceeded* <br/> *Recusa* <br/> *Limpos* |
| Descrição        | Valores de cadeia de caracteres para *StatusCode* |
| DeviceID           | A  DeviceID do dispositivo de destino da mensagem da nuvem para o dispositivo à qual esse comentário se relaciona |
| DeviceGenerationId | O *DeviceGenerationId* do dispositivo de destino da mensagem da nuvem para o dispositivo à qual esse comentário se relaciona |

Para a mensagem da nuvem para o dispositivo correlacionar seus comentários com a mensagem original, o serviço deve especificar uma *MessageId*.

O corpo de uma mensagem de comentários é mostrado no código a seguir:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Opções de configuração da nuvem para o dispositivo

Cada Hub IoT expõe as seguintes opções de configuração para mensagens de nuvem para o dispositivo:

| Propriedade                  | Descrição | Intervalo e padrão |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL padrão para mensagens da nuvem para o dispositivo | Intervalo de ISO_8601 de até 2 dias (mínimo de 1 minuto); os 1 hora |
| maxDeliveryCount          | Contagem máxima de entregas para filas de nuvem para dispositivo por dispositivo | 1 a 100; os 10 |
| feedback.ttlAsIso8601     | Retenção de mensagens de comentários associadas ao serviço | Intervalo de ISO_8601 de até 2 dias (mínimo de 1 minuto); os 1 hora |
| feedback.maxDeliveryCount | Contagem máxima de entregas para a fila de comentários | 1 a 100; os 100 |

Para obter mais informações sobre como configurar essas opções de configuração, consulte [Criar Hubs IoT](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre os SDKs que você pode usar para receber mensagens da nuvem para o dispositivo, consulte [SDKs do IOT do Azure](iot-hub-devguide-sdks.md).

Para experimentar a recepção de mensagens de nuvem para dispositivo, consulte o tutorial [Enviar de nuvem para dispositivo ](iot-hub-csharp-csharp-c2d.md).

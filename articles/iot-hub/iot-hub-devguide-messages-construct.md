---
title: Entender o formato de mensagem do Hub IoT do Azure| Microsoft Docs
description: "Guia do desenvolvedor - descreve o formato e o conteúdo esperado de mensagens do Hub IoT."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.openlocfilehash: 793a2cdd55c4e9213e5097f4e318c0790960a64d
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-read-iot-hub-messages"></a>Criar e ler mensagens do Hub IoT

Para suportar a interoperabilidade contínua entre protocolos, o Hub IoT define um formato de mensagem comum para todos os protocolos voltados para o dispositivo. Este formato de mensagem é utilizado para as mensagens [do dispositivo para a nuvem][lnk-d2c] e [nuvem para o dispositivo][lnk-c2d]. Uma [mensagem do Hub IoT][lnk-messaging] consiste de:

* Um conjunto de *propriedades do sistema*. Propriedades que o Hub IoT interpreta ou define. Esse conjunto é predeterminado.
* Um conjunto de *propriedades do aplicativo*. Um dicionário de propriedades de cadeia de caracteres que o aplicativo pode definir e acessar sem precisar desserializar o corpo da mensagem. O Hub IoT nunca modifica essas propriedades.
* Um corpo de binário opaco.

Valores e nomes de propriedade podem conter somente caracteres alfanuméricos ASCII, mais ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` quando você:

* Envia mensagens do dispositivo para a nuvem usando o protocolo HTTPS.
* Enviar mensagens da nuvem para o dispositivo.

Para obter mais informações sobre como codificar e decodificar mensagens enviadas utilizando diferentes protocolos, consulte [SDKs do IoT do Azure][lnk-sdks].

A tabela a seguir lista o conjunto de propriedades do sistema em mensagens do Hub IoT.

| Propriedade | Descrição |
| --- | --- |
| MessageId |Um identificador configurável pelo usuário para a mensagem utilizada para padrões de resposta à solicitação. Formato: uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de sequência |Um número (exclusivo por fila de dispositivos) atribuído pelo Hub IoT a cada mensagem da nuvem para o dispositivo. |
| Para |Um destino especificado em mensagens [Da nuvem para o dispositivo][lnk-c2d]. |
| ExpiryTimeUtc |Data e hora de expiração da mensagem. |
| EnqueuedTime |Data e hora do recebimento da mensagem [Nuvem para o Dispositivo][lnk-c2d] pelo Hub IoT. |
| CorrelationId |Uma cadeia de propriedade em uma mensagem de resposta que geralmente contém a MessageId da solicitação em padrões de solicitação-resposta. |
| UserId |Uma ID usada para especificar a origem das mensagens. Quando as mensagens são geradas pelo Hub IoT, são definidas como `{iot hub name}`. |
| Ack |Um gerador de mensagem de comentários. Essa propriedade é usada em mensagens da nuvem para o dispositivo para solicitar ao Hub IoT a geração de mensagens de comentários como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: **nenhum** (padrão): nenhuma mensagem de comentários é gerada, **positivo**: ocorrerá o recebimento de uma mensagem de comentários se a mensagem estiver completa, **negativo**: ocorrerá o recebimento de uma mensagem de comentários se a mensagem expirar (ou se a contagem máxima de entrega tiver sido atingida) sem ser concluída pelo dispositivo ou **total**: positivos e negativos. Para saber mais, consulte [Comentários sobre a mensagem][lnk-feedback]. |
| ConnectionDeviceId |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **deviceId** do dispositivo que enviou a mensagem. |
| ConnectionDeviceGenerationId |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **generationId** (de acordo com as [Propriedades de identidade de dispositivo][lnk-device-properties]) do dispositivo que enviou a mensagem. |
| ConnectionAuthMethod |Um método de autenticação definido pelo Hub IoT em mensagens do dispositivo para a nuvem. Essa propriedade contém informações sobre o método de autenticação usado para autenticar o dispositivo que envia a mensagem. Para saber mais, veja [Antifalsificação do dispositivo para a nuvem][lnk-antispoofing]. |

## <a name="message-size"></a>Tamanho da mensagem

O Hub IoT mede o tamanho da mensagem independentemente do protocolo, considerando apenas a carga real. O tamanho em bytes é calculado como a soma do seguinte:

* O tamanho do corpo em bytes.
* O tamanho em bytes de todos os valores das propriedades do sistema de mensagens.
* O tamanho em bytes de todos os nomes e valores de propriedade do usuário.

Valores e nomes de propriedade são limitados a caracteres ASCII, portanto, o comprimento das cadeias de caracteres é igual ao tamanho em bytes.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre limites de tamanho de mensagem no Hub IoT, consulte [cotas do Hub IoT e limitação][lnk-quotas].

Para saber como criar e ler mensagens do Hub IoT em várias linguagens de programação, consulte os tutoriais de [Introdução][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties

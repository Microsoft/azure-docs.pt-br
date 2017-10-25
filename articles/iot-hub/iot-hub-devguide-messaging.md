---
title: Entender as mensagens do Hub IoT do Azure| Microsoft Docs
description: "Guia do desenvolvedor – Mensagens do dispositivo para a nuvem e da nuvem para o dispositivo com o Hub IoT. Inclui informações sobre formatos de mensagem e protocolos de comunicação com suporte."
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
ms.date: 09/14/2017
ms.author: dobett
ms.openlocfilehash: 4a7f545f1af2904f47808fe8b1d7d3aab3afff84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT

Use mensagens do Hub IoT para se comunicar com os dispositivos da seguinte forma:

* Enviando mensagens de [dispositivo para nuvem][lnk-d2c] de dispositivos para a solução de back-end.
* Enviando mensagens de [nuvem para dispositivo][lnk-c2d] da solução de back-end para os dispositivos.

As propriedades básicas da funcionalidade de mensagens do Hub IoT são a confiabilidade e a durabilidade das mensagens. Essas propriedades habilitam a adaptação à conectividade intermitente no lado do dispositivo e a picos de carga no processamento de eventos no lado da nuvem. O Hub IoT implementa *pelo menos uma vez* as garantias de entrega de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo.

Para obter uma introdução aos recursos de Hub IoT, confira os artigos [Azure e Internet das Coisas][lnk-azure-iot] e [Visão geral do serviço do Hub IoT do Azure][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Quando usar mensagens do Hub IoT

Use mensagens de dispositivo para nuvem a fim de enviar alertas e telemetria de série temporal de seu aplicativo de dispositivo e de nuvem para dispositivo no caso de notificações unidirecionais para o aplicativo do dispositivo.

* Veja as[diretrizes de comunicação do dispositivo para a nuvem][lnk-d2c-guidance] se está em dúvida entre o uso de mensagens do dispositivo para a nuvem, propriedades reportadas ou carregamento do arquivo.
* Veja as [diretrizes de comunicação da nuvem para o dispositivo][lnk-c2d-guidance] se está em dúvida entre o uso de mensagens da nuvem para o dispositivo, propriedades desejadas ou métodos diretos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [mensagens de dispositivo para a nuvem][lnk-d2c] do Hub IoT.
* Saiba mais sobre [mensagens da nuvem para dispositivos][lnk-c2d] do Hub IoT.

[lnk-azure-iot]: iot-hub-what-is-azure-iot.md
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
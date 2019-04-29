---
title: Opções da nuvem para o dispositivo do Hub IoT do Azure| Microsoft Docs
description: Guia de desenvolvedor ‑ diretrizes sobre quando usar métodos diretos, propriedades desejadas do dispositivo gêmeo ou mensagens para comunicações da nuvem para o dispositivo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 4b738f34ae75478c0120832e7ad2b6a6a83dbf69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224754"
---
# <a name="cloud-to-device-communications-guidance"></a>Diretrizes de comunicações da nuvem para o dispositivo

O Hub IoT fornece três opções para os aplicativos de dispositivos exporem funcionalidades a um aplicativo de back-end:

* [Métodos diretos](iot-hub-devguide-direct-methods.md) para comunicações que exigem confirmação imediata do resultado. Direcionar métodos é muitas vezes usado para controle interativo de dispositivos, como ativar um ventilador.

* [Propriedades desejadas do gêmeo](iot-hub-devguide-device-twins.md) para comandos de longa duração que têm o objetivo de colocar o dispositivo em determinado estado desejado. Por exemplo, defina o intervalo de envio de telemetria como 30 minutos.

* [Mensagens da nuvem para o dispositivo](iot-hub-devguide-messages-c2d.md) para notificações unidirecionais para o aplicativo do dispositivo.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Aqui está uma comparação detalhada das várias opções de comunicação da nuvem para o dispositivo.

|  | Métodos diretos | Propriedades desejadas do gêmeo | Mensagens da nuvem para o dispositivo |
| ---- | ------- | ---------- | ---- |
| Cenário | Comandos que exigem confirmação imediata, por exemplo, ligar um ventilador. | Comandos de longa duração que têm o objetivo de colocar o dispositivo em um determinado estado desejado. Por exemplo, defina o intervalo de envio de telemetria como 30 minutos. | Notificações unidirecionais para o aplicativo do dispositivo. |
| Fluxo de dados | Bidirecional. O aplicativo do dispositivo pode responder imediatamente ao método. O back-end da solução recebe o resultado de acordo com o contexto da solicitação. | Unidirecional. O aplicativo do dispositivo recebe uma notificação com a alteração da propriedade. | Unidirecional. O aplicativo do dispositivo recebe a mensagem
| Durabilidade | Dispositivos desconectados não são contatados. O back-end da solução é notificado de que o dispositivo não está conectado. | Os valores de propriedade são preservados no dispositivo gêmeo. O dispositivo lerá na próxima reconexão. Os valores da propriedade podem ser recuperados com a [linguagem de consulta do Hub IoT](iot-hub-devguide-query-language.md). | As mensagens podem ser mantidas pelo Hub IoT por até 48 horas. |
| Destinos | Dispositivo único usando **deviceId** ou vários dispositivos usando [jobs](iot-hub-devguide-jobs.md). | Dispositivo único usando **deviceId** ou vários dispositivos usando [jobs](iot-hub-devguide-jobs.md). | Dispositivo único por **deviceId**. |
| Tamanho | O tamanho de payload do método direto máximo é 128 KB. | O tamanho máximo desejado das propriedades é de 8 KB. | Mensagens de até 64 KB. |
| Frequência | Alta. Para obter mais informações, confira [Limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). | Média. Para obter mais informações, confira [Limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). | Baixa. Para obter mais informações, confira [Limites do Hub IoT](iot-hub-devguide-quotas-throttling.md). |
| Protocolo | Disponível usando MQTT ou AMQP. | Disponível usando MQTT ou AMQP. | Disponível em todos os protocolos. O dispositivo deve sondar ao usar HTTPS. |

Saiba como usar métodos diretos, propriedades desejadas e mensagens da nuvem para o dispositivo nos seguintes tutoriais:

* [Usar métodos diretos](quickstart-control-device-node.md)
* [Usar as propriedades desejadas para configurar os dispositivos](tutorial-device-twins.md) 
* [Enviar mensagens da nuvem para o dispositivo](iot-hub-node-node-c2d.md)
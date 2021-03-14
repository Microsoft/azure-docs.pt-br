---
title: Alertas de segurança personalizados para o Hub IoT
description: Saiba mais sobre alertas de segurança personalizáveis e correção recomendada usando o defender para recursos e serviços do Hub IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/16/2021
ms.author: shhazam
ms.openlocfilehash: 04198432f2b600a3c703d5e4f253656f116000db
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636519"
---
# <a name="defender-for-iot-hub-custom-security-alerts"></a>Alertas de segurança personalizados do defender para Hub IoT

O defender para IoT analisa continuamente sua solução de IoT usando a análise avançada e a inteligência contra ameaças para alertá-lo sobre atividades mal-intencionadas.

Incentivamos você a criar alertas personalizados com base em seu conhecimento do comportamento de dispositivo esperado para garantir que os alertas atuem como os indicadores mais eficientes de comprometimento potencial em sua implantação e cenário de organização exclusivos.

As seguintes listas do defender para alertas de IoT são definíveis por você com base no comportamento esperado do Hub IoT. Para obter mais informações sobre como personalizar cada alerta, consulte [criar alertas personalizados](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Alertas personalizados internos no Hub IoT

| Severidade | Nome do alerta | Fonte de dados | Descrição | Correção sugerida |
|--|--|--|--|--|
| Baixo | Alerta personalizado-o número de mensagens de nuvem para dispositivo no protocolo AMQP está fora do intervalo permitido | Hub IoT | O número de mensagens de nuvem para dispositivo (protocolo AMQP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado-o número de mensagens de nuvem rejeitadas para o dispositivo no protocolo AMQP está fora do intervalo permitido | Hub IoT | O número de mensagens de nuvem para dispositivo (protocolo AMQP) rejeitado pelo dispositivo, em uma janela de tempo específica, está fora do intervalo configurado e permitido no momento. |  |
| Baixo | Alerta personalizado-o número de mensagens do dispositivo para a nuvem no protocolo AMQP está fora do intervalo permitido | Hub IoT | O número de mensagens do dispositivo para a nuvem (protocolo AMQP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado – o número de invocações de método direto está fora do intervalo permitido | Hub IoT | O número de invocações de método direto em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado – o número de carregamentos de arquivos está fora do intervalo permitido | Hub IoT | O número de uploads de arquivo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado-o número de mensagens de nuvem para o dispositivo no protocolo HTTP está fora do intervalo permitido | Hub IoT | A quantidade de mensagens da nuvem para o dispositivo (protocolo HTTP) em uma janela de tempo não está no intervalo configurado permitido |
| Baixo | Alerta personalizado-o número de mensagens de nuvem rejeitadas para o dispositivo no protocolo HTTP não está no intervalo permitido | Hub IoT | O número de mensagens da nuvem para o dispositivo (protocolo HTTP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |
| Baixo | Alerta personalizado-o número de mensagens do dispositivo para a nuvem no protocolo HTTP está fora do intervalo permitido | Hub IoT | A quantidade de dispositivo para mensagens de nuvem (protocolo HTTP) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado-o número de mensagens de nuvem para dispositivo no protocolo MQTT está fora do intervalo permitido | Hub IoT | O número de mensagens da nuvem para o dispositivo (protocolo MQTT) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado-o número de mensagens de nuvem rejeitadas para o dispositivo no protocolo MQTT está fora do intervalo permitido | Hub IoT | O número de mensagens de nuvem para dispositivo (protocolo MQTT) rejeitadas pelo dispositivo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |
| Baixo | Alerta personalizado-o número de mensagens do dispositivo para a nuvem no protocolo MQTT está fora do intervalo permitido | Hub IoT | O número de mensagens do dispositivo para a nuvem (protocolo MQTT) em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |
| Baixo | Alerta personalizado-o número de limpezas de fila de comando que estão fora do intervalo permitido | Hub IoT | O número de limpezas de fila de comando em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado-o número de atualizações do módulo de atualização está fora do intervalo permitido | Hub IoT | O número de atualizações de módulo gêmeo em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |
| Baixo | Alerta personalizado-o número de operações não autorizadas está fora do intervalo permitido | Hub IoT | O número de operações não autorizadas em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Personalizar um alerta](quickstart-create-custom-alerts.md)
- [Visão geral](overview.md) do serviço defender para IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)

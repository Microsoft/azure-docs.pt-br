---
title: Alertas de segurança personalizados
description: Saiba mais sobre alertas de segurança personalizáveis e correção recomendada usando o defender para recursos e serviços de IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 021b05176da141fad2f4555b0617b5a4a51e453f
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809279"
---
# <a name="defender-for-iot-custom-security-alerts"></a>Alertas de segurança personalizados do defender para IoT

O defender para IoT analisa continuamente sua solução de IoT usando a análise avançada e a inteligência contra ameaças para alertá-lo sobre atividades mal-intencionadas.

Incentivamos você a criar alertas personalizados com base em seu conhecimento do comportamento de dispositivo esperado para garantir que os alertas atuem como os indicadores mais eficientes de comprometimento potencial em sua implantação e cenário de organização exclusivos.

As seguintes listas de alertas do defender for IoT são definíveis por você com base no comportamento esperado do Hub IoT e/ou do dispositivo. Para obter mais informações sobre como personalizar cada alerta, consulte [criar alertas personalizados](quickstart-create-custom-alerts.md).

## <a name="built-in-custom-alerts-in-the-iot-hub"></a>Alertas personalizados internos no Hub IoT

| Severity | Nome do alerta | Fonte de dados | Descrição | Correção sugerida |
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


## <a name="agent-based-security-custom-alerts"></a>Alertas personalizados de segurança baseados em agente

| Severity | Nome do alerta | Fonte de dados | Descrição | Correção sugerida |
|--|--|--|--|--|
| Baixo | Alerta personalizado – o número de conexões ativas está fora do intervalo permitido | Módulo de segurança clássico, RTOS do Azure | O número de conexões ativas em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. | Investigue os logs do dispositivo. Saiba onde a conexão foi originada e determine se ela é benigna ou mal-intencionada. Se for mal-intencionada, remova o possível malware e entenda a origem. Se for benigna, adicione a origem à lista de conexões permitidas. |
| Baixo | Alerta personalizado-a conexão de saída criada para um IP que não é permitido | Módulo de segurança clássico, RTOS do Azure | Uma conexão de saída foi criada para um IP que está fora da sua lista de IPs permitidos. | Investigue os logs do dispositivo. Saiba onde a conexão foi originada e determine se ela é benigna ou mal-intencionada. Se for mal-intencionada, remova o possível malware e entenda a origem. Se for benigna, adicione a origem à lista de IPs permitidos. |
| Baixo | Alerta personalizado-o número de logons locais com falha está fora do intervalo permitido | Módulo de segurança clássico, RTOS do Azure | O número de logons locais com falha em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado-a entrada de um usuário que não está na lista de usuários permitidos | Módulo de segurança clássico, RTOS do Azure | Um usuário local fora da lista de usuários permitidos, conectado ao dispositivo. | Se você estiver salvando dados brutos, navegue até sua conta do log Analytics e use os dados para investigar o dispositivo, identifique a origem e, em seguida, corrija a lista de permissões/bloqueios para essas configurações. Se você não estiver salvando dados brutos no momento, vá para o dispositivo e corrija a lista de permissões/bloqueios para essas configurações. |
| Baixo | Alerta personalizado-um processo foi executado e não é permitido | Módulo de segurança clássico, RTOS do Azure | Um processo não permitido foi executado no dispositivo. | Se você estiver salvando dados brutos, navegue até sua conta do log Analytics e use os dados para investigar o dispositivo, identifique a origem e, em seguida, corrija a lista de permissões/bloqueios para essas configurações. Se você não estiver salvando dados brutos no momento, vá para o dispositivo e corrija a lista de permissões/bloqueios para essas configurações. |
|

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Personalizar um alerta](quickstart-create-custom-alerts.md)
- [Visão geral](overview.md) do serviço defender para IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)

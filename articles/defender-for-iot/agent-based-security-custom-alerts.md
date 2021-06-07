---
title: Alertas personalizados de segurança baseados em agente
description: Saiba mais sobre alertas de segurança personalizáveis e correção recomendada usando o defender para recursos e serviço do dispositivo IoT.
ms.topic: conceptual
ms.date: 2/16/2021
ms.openlocfilehash: 2fb1385c12cbd9d0479d8528f54aad8816393ad1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784910"
---
# <a name="defender-for-iot-devices-custom-security-alerts"></a>Alertas de segurança personalizados para dispositivos do defender for IoT

O defender para IoT analisa continuamente sua solução de IoT usando a análise avançada e a inteligência contra ameaças para alertá-lo sobre atividades mal-intencionadas.

Incentivamos você a criar alertas personalizados com base em seu conhecimento do comportamento de dispositivo esperado para garantir que os alertas atuem como os indicadores mais eficientes de comprometimento potencial em sua implantação e cenário de organização exclusivos.

As seguintes listas do defender para alertas de IoT são definíveis por você com base no comportamento esperado do dispositivo IoT. Para obter mais informações sobre como personalizar cada alerta, consulte [criar alertas personalizados](quickstart-create-custom-alerts.md).

## <a name="agent-based-security-custom-alerts"></a>Alertas personalizados de segurança baseados em agente

| Severidade | Nome do alerta | Fonte de dados | Descrição | Correção sugerida |
|--|--|--|--|--|
| Baixo | Alerta personalizado – o número de conexões ativas está fora do intervalo permitido | Defender clássico-IoT-micro-Agent, RTOS do Azure | O número de conexões ativas em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. | Investigue os logs do dispositivo. Saiba onde a conexão foi originada e determine se ela é benigna ou mal-intencionada. Se for mal-intencionada, remova o possível malware e entenda a origem. Se for benigna, adicione a origem à lista de conexões permitidas. |
| Baixo | Alerta personalizado-a conexão de saída criada para um IP que não é permitido | Defender clássico-IoT-micro-Agent, RTOS do Azure | Uma conexão de saída foi criada para um IP que está fora da sua lista de IPs permitidos. | Investigue os logs do dispositivo. Saiba onde a conexão foi originada e determine se ela é benigna ou mal-intencionada. Se for mal-intencionada, remova o possível malware e entenda a origem. Se for benigna, adicione a origem à lista de IPs permitidos. |
| Baixo | Alerta personalizado-o número de logons locais com falha está fora do intervalo permitido | Defender clássico-IoT-micro-Agent, RTOS do Azure | O número de logons locais com falha em uma janela de tempo específica está fora do intervalo atualmente configurado e permitido. |  |
| Baixo | Alerta personalizado-a entrada de um usuário que não está na lista de usuários permitidos | Defender clássico-IoT-micro-Agent, RTOS do Azure | Um usuário local fora da lista de usuários permitidos, conectado ao dispositivo. | Se você estiver salvando dados brutos, navegue até sua conta do log Analytics e use os dados para investigar o dispositivo, identifique a origem e, em seguida, corrija a lista de permissões/bloqueios para essas configurações. Se você não estiver salvando dados brutos no momento, vá para o dispositivo e corrija a lista de permissões/bloqueios para essas configurações. |
| Baixo | Alerta personalizado-um processo foi executado e não é permitido | Defender clássico-IoT-micro-Agent, RTOS do Azure | Um processo não permitido foi executado no dispositivo. | Se você estiver salvando dados brutos, navegue até sua conta do log Analytics e use os dados para investigar o dispositivo, identifique a origem e, em seguida, corrija a lista de permissões/bloqueios para essas configurações. Se você não estiver salvando dados brutos no momento, vá para o dispositivo e corrija a lista de permissões/bloqueios para essas configurações. |
|

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Personalizar um alerta](quickstart-create-custom-alerts.md)
- [Visão geral](overview.md) do serviço defender para IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)

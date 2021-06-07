---
title: Recomendações baseadas em agente
description: Saiba mais sobre o conceito de recomendações de segurança e como elas são usadas para dispositivos do defender para IoT.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a29957f459edad8b768daea7bc4567d77c80b165
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784978"
---
# <a name="security-recommendations-for-iot-devices"></a>Recomendações de segurança para dispositivos IoT

O defender para IoT examina seus recursos do Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a superfície de ataque.
As recomendações de segurança são acionáveis e visam ajudar os clientes a cumprir as práticas recomendadas de segurança.

Neste artigo, você encontrará uma lista de recomendações, que podem ser disparadas em seus dispositivos IoT.

## <a name="agent-based-recommendations"></a>Recomendações baseadas em agente

As recomendações de dispositivo fornecem ideias e sugestões para melhorar a postura de segurança do dispositivo.

| Severidade | Nome | fonte de dados | Descrição |
|--|--|--|--|
| Médio | Abrir portas no dispositivo | Defender clássico-IoT-micro-Agent| Um ponto de extremidade de escuta foi encontrado no dispositivo. |
| Médio | Política de firewall permissiva encontrada em uma das cadeias. | Defender clássico-IoT-micro-Agent| Política de firewall permitida encontrada (entrada/saída). A política de firewall deve negar todo o tráfego por padrão e definir regras para permitir a comunicação necessária de/para o dispositivo. |
| Médio | A regra de firewall permissiva na cadeia de entrada foi encontrada | Defender clássico-IoT-micro-Agent| Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas. |
| Médio | A regra de firewall permissiva na cadeia de saída foi encontrada | Defender clássico-IoT-micro-Agent| Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas. |
| Médio | Falha na validação da linha de base do sistema operacional | Defender clássico-IoT-micro-Agent| O dispositivo não está em conformidade com os [benchmarks Linux do CIS](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Recomendações operacionais baseadas em agente

As recomendações operacionais fornecem informações e sugestões para melhorar a configuração do agente de segurança.

| Severidade | Nome | fonte de dados | Descrição |
|--|--|--|--|
| Baixo | O Agent envia mensagens não utilizadas | Defender clássico-IoT-micro-Agent | 10% ou mais de mensagens de segurança foram menores que 4 KB durante as últimas 24 horas. |
| Baixo | Configuração de configurações de segurança não ideal | Defender clássico-IoT-micro-Agent | A configuração de configurações de segurança não é ideal. |
| Baixo | Conflito de configuração de configurações de segurança | Defender clássico-IoT-micro-Agent | Os conflitos foram identificados na configuração de configurações de segurança. |

## <a name="next-steps"></a>Próximas etapas

- [Visão geral](overview.md) do serviço defender para IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)

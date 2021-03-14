---
title: Recomendações baseadas em agente
titleSuffix: Azure Defender for IoT
description: Saiba mais sobre o conceito de recomendações de segurança e como elas são usadas para dispositivos do defender para IoT.
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
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: c7407db1460c2d0a83f2262a3348c81b4de300a7
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642328"
---
# <a name="security-recommendations-for-iot-devices"></a>Recomendações de segurança para dispositivos IoT

O defender para IoT examina seus recursos do Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a superfície de ataque.
As recomendações de segurança são acionáveis e visam ajudar os clientes a cumprir as práticas recomendadas de segurança.

Neste artigo, você encontrará uma lista de recomendações, que podem ser disparadas em seus dispositivos IoT.

## <a name="agent-based-recommendations"></a>Recomendações baseadas em agente

As recomendações de dispositivo fornecem ideias e sugestões para melhorar a postura de segurança do dispositivo.

| Severidade | Nome | fonte de dados | Descrição |
|--|--|--|--|
| Média | Abrir portas no dispositivo | Módulo de segurança clássico | Um ponto de extremidade de escuta foi encontrado no dispositivo. |
| Média | Política de firewall permissiva encontrada em uma das cadeias. | Módulo de segurança clássico | Política de firewall permitida encontrada (entrada/saída). A política de firewall deve negar todo o tráfego por padrão e definir regras para permitir a comunicação necessária de/para o dispositivo. |
| Média | A regra de firewall permissiva na cadeia de entrada foi encontrada | Módulo de segurança clássico | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas. |
| Média | A regra de firewall permissiva na cadeia de saída foi encontrada | Módulo de segurança clássico | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas. |
| Média | Falha na validação da linha de base do sistema operacional | Módulo de segurança clássico | O dispositivo não está em conformidade com os [benchmarks Linux do CIS](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Recomendações operacionais baseadas em agente

As recomendações operacionais fornecem informações e sugestões para melhorar a configuração do agente de segurança.

| Severidade | Nome | fonte de dados | Descrição |
|--|--|--|--|
| Baixo | O Agent envia mensagens não utilizadas | Módulo de segurança clássico | 10% ou mais de mensagens de segurança foram menores que 4 KB durante as últimas 24 horas. |
| Baixo | Configuração de configurações de segurança não ideal | Módulo de segurança clássico | A configuração de configurações de segurança não é ideal. |
| Baixo | Conflito de configuração de configurações de segurança | Módulo de segurança clássico | Os conflitos foram identificados na configuração de configurações de segurança. |  |

## <a name="next-steps"></a>Próximas etapas

- [Visão geral](overview.md) do serviço defender para IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)

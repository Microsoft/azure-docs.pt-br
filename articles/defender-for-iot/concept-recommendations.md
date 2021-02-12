---
title: Recomendações de segurança
description: Saiba mais sobre o conceito de recomendações de segurança e como elas são usadas no defender para IoT.
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
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: d11154e26e2ffe97a1ea102a11dddb746db9433a
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809262"
---
# <a name="security-recommendations"></a>Recomendações de segurança

O defender para IoT examina seus recursos do Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a superfície de ataque.
As recomendações de segurança são acionáveis e visam ajudar os clientes a cumprir as práticas recomendadas de segurança.

Neste artigo, você encontrará uma lista de recomendações, que podem ser disparadas em seus dispositivos de Hub IoT e/ou IoT.

## <a name="agent-based-recommendations"></a>Recomendações baseadas em agente

As recomendações de dispositivo fornecem ideias e sugestões para melhorar a postura de segurança do dispositivo.

| Severity | Nome | fonte de dados | Descrição |
|--|--|--|--|
| Médio | Abrir portas no dispositivo | Módulo de segurança clássico | Um ponto de extremidade de escuta foi encontrado no dispositivo. |
| Médio | Política de firewall permissiva encontrada em uma das cadeias. | Módulo de segurança clássico | Política de firewall permitida encontrada (entrada/saída). A política de firewall deve negar todo o tráfego por padrão e definir regras para permitir a comunicação necessária de/para o dispositivo. |
| Médio | A regra de firewall permissiva na cadeia de entrada foi encontrada | Módulo de segurança clássico | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas. |
| Médio | A regra de firewall permissiva na cadeia de saída foi encontrada | Módulo de segurança clássico | Foi encontrada uma regra no firewall que contém um padrão permissivo para uma grande variedade de endereços IP ou portas. |
| Médio | Falha na validação da linha de base do sistema operacional | Módulo de segurança clássico | O dispositivo não está em conformidade com os [benchmarks Linux do CIS](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Recomendações operacionais baseadas em agente

As recomendações operacionais fornecem informações e sugestões para melhorar a configuração do agente de segurança.

| Severity | Nome | fonte de dados | Descrição |
|--|--|--|--|
| Baixo | O Agent envia mensagens não utilizadas | Módulo de segurança clássico | 10% ou mais de mensagens de segurança foram menores que 4 KB durante as últimas 24 horas. |
| Baixo | Configuração de configurações de segurança não ideal | Módulo de segurança clássico | A configuração de configurações de segurança não é ideal. |
| Baixo | Conflito de configuração de configurações de segurança | Módulo de segurança clássico | Os conflitos foram identificados na configuração de configurações de segurança. |  |


## <a name="built-in-recommendations-in-iot-hub"></a>Recomendações internas no Hub IoT

Alertas de recomendação fornecem Insight e sugestões para ações para melhorar a postura de segurança do seu ambiente.

| Severity | Nome | fonte de dados | Descrição |
|--|--|--|--|
| Alta | Credenciais de autenticação idênticas usadas por vários dispositivos | Hub IoT | As credenciais de autenticação do Hub IoT são usadas por vários dispositivos. Esse processo pode indicar um dispositivo ilegítimo representando um dispositivo legítimo. O uso de credenciais duplicadas aumenta o risco de representação do dispositivo por um ator mal-intencionado. |
| Médio | A política de filtro IP padrão deve ser Deny | Hub IoT | A configuração do filtro IP deve ter regras definidas para tráfego permitido e, por padrão, negar todos os outros tráfego por padrão. |
| Médio | A regra de filtro IP inclui um intervalo IP grande | Hub IoT | Um intervalo de IP de origem de regra de filtro IP de permissão é muito grande. Regras excessivamente permissivas podem expor seu hub IoT para atores mal-intencionados. |
| Baixo | Habilitar logs de diagnóstico no Hub IoT | Hub IoT | Habilite os logs e retenha-os por até um ano. A retenção de logs permite recriar trilhas de atividade para fins de investigação quando ocorre um incidente de segurança ou sua rede é comprometida. |


## <a name="next-steps"></a>Próximas etapas

- [Visão geral](overview.md) do serviço defender para IOT
- Saiba como [acessar seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)

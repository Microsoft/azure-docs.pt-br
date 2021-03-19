---
title: Visão geral do portfólio de agentes e suporte do sistema operacional
description: O Azure Defender para IoT oferece um amplo portfólio de agentes com base no tipo de dispositivo.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: e2897018d1695bde665e1d1aca180e5268851a0b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120140"
---
# <a name="agent-portfolio-overview-and-os-support"></a>Visão geral do portfólio de agentes e suporte do sistema operacional 

O Azure Defender para IoT oferece um amplo portfólio de agentes com base no tipo de dispositivo. 

## <a name="standalone-agent"></a>Agente autônomo

O agente autônomo abrange a maioria dos sistemas operacionais Linux, que podem ser implantados como um pacote binário ou como um código-fonte que pode ser incorporado como parte do firmware e permitir a modificação e a personalização de acordo com as necessidades do cliente. Um exemplo de suporte do sistema operacional: 

| Sistema operacional | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

Para obter mais detalhes, suporte ao sistema operacional ou para solicitar acesso ao código-fonte de modo que você possa incorporá-lo como parte do firmware do dispositivo, entre em contato com o seu gerente de conta ou envie um email para <defender_micro_agent@microsoft.com>. 

## <a name="azure-rtos-micro-agent"></a>Microagente do Azure RTOS

O microagente do Azure Defender para IoT fornece uma solução de segurança abrangente e leve para os dispositivos que usam o Azure RTOS. O microagente do Azure Defender para IoT fornece cobertura para ameaças comuns e possíveis atividades mal-intencionadas em dispositivos RTOS (sistema operacional em tempo real). O microagente vem integrado como parte do componente Azure RTOS NetX Duo e monitora a atividade de rede do dispositivo. 

O microagente do Azure Defender para IoT vem integrado como parte do componente Azure RTOS NetX Duo e monitora a atividade de rede do dispositivo. O microagente consiste em uma solução de segurança abrangente e leve que fornece cobertura para ameaças comuns e possíveis atividades mal-intencionadas em dispositivos RTOS (sistema operacional em tempo real).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [Visão geral do microagente autônomo ](concept-standalone-micro-agent-overview.md).
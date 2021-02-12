---
title: Visão geral dos agentes de segurança
description: Entenda a arquitetura do agente de segurança para os agentes usados no serviço Azure defender para IoT.
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
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: ff837fe88f878c522366b2b6bc19a1ef3954b667
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820646"
---
# <a name="security-agent-reference-architecture"></a>Arquitetura de referência do agente de segurança

O Azure defender para IoT fornece arquitetura de referência para agentes de segurança que registram, processam, agregam e enviam dados de segurança por meio do Hub IoT.

Os agentes de segurança são projetados para funcionar em um ambiente de IoT restrito e são altamente personalizáveis em termos de valores que eles fornecem quando comparados aos recursos que consomem.

Os agentes de segurança oferecem suporte aos seguintes recursos:

- Autentique com a identidade do dispositivo existente ou uma identidade de módulo dedicada. Para saber mais, consulte [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md).

- Coletar eventos de segurança brutos do sistema operacional subjacente (Linux, Windows). Para saber mais sobre os coletores de dados de segurança disponíveis, confira [configuração do agente do defender para IOT](how-to-agent-configuration.md).

- Agregar eventos de segurança brutos em mensagens enviadas por meio do Hub IoT.

- Configure remotamente por meio do uso do módulo **azureiotsecurity** . Para saber mais, confira [configurar um agente do defender para IOT](how-to-agent-configuration.md).

Os agentes de segurança do defender para IoT são desenvolvidos como projetos de código-fonte aberto e estão disponíveis no GitHub:

- [Defender para agente baseado em IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Agente baseado no defender para IoT C#](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plataformas com suporte do agente

O defender para IoT oferece agentes de instalador diferentes para o Windows de 32 bits e 64 bits e o mesmo para o Linux de 32 bits e de 64 bits. Verifique se você tem o instalador do agente correto para cada um dos seus dispositivos de acordo com a tabela a seguir:

| Arquitetura | Linux | Windows | Detalhes |
|--|--|--|--|
| 32 bits | C | C# |  |
| 64 bits | C# ou C | C# | É recomendável usar o agente do C para dispositivos com recursos de dispositivo mais restritos ou mínimos. |


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você tem uma visão geral de alto nível sobre a arquitetura do módulo de segurança do defender para IoT e os instaladores disponíveis.

Para continuar a introdução à implantação do defender para IoT, use os seguintes artigos:

- Entender os [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecionar e implantar um [agente de segurança](how-to-deploy-agent.md)
- Examinar os [pré-requisitos do sistema](quickstart-system-prerequisites.md) do defender for IOT
- Saiba como [habilitar o defender para o serviço de IOT em seu hub IOT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [defender for IOT FAQ](resources-frequently-asked-questions.md)

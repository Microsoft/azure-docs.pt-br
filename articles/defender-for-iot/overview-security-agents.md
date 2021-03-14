---
title: Agentes de segurança
description: Comece a entender, configurar, implantar e usar o Azure defender para agentes de serviço de segurança de IoT em seus dispositivos IoT.
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
ms.date: 1/24/2021
ms.author: shhazam
ms.openlocfilehash: a40b64dd3b8f898c961863c1d78a2a56642c44b9
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521604"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Introdução ao Azure defender para micro agentes de dispositivo IoT

Agentes de segurança do defender para IoT oferecem recursos de segurança aprimorados, como monitoramento de práticas recomendadas de configuração do sistema operacional. Assuma o controle do seu campo de dispositivo proteção contra ameaças e postura de segurança com um único serviço.

Os agentes de segurança dos defensores da IoT lidam com a coleta de eventos brutos do sistema operacional do dispositivo, agregação de eventos para reduzir o custo e a configuração por meio de um módulo de dispositivo. As mensagens de segurança são enviadas por meio do Hub IoT para o defender para serviços de análise de IoT.

Use o seguinte fluxo de trabalho para implantar e testar seus agentes de segurança do defender para IoT:

1. [Habilite o defender para o serviço de IOT para o Hub IOT](quickstart-onboard-iot-hub.md).

1. Se o Hub IoT não tiver nenhum dispositivo registrado, [registre um novo dispositivo](../iot-accelerators/iot-accelerators-device-simulation-overview.md).

1. [Crie um módulo DefenderIotMicroAgent](quickstart-create-micro-agent-module-twin.md) para seus dispositivos.

1. Para instalar o agente em um dispositivo simulado do Azure em vez de instalar em um dispositivo real, [crie uma nova VM (máquina virtual) do Azure](../virtual-machines/linux/quick-create-portal.md) em uma zona disponível.

1. [Implante um agente de segurança do defender para IOT](how-to-deploy-linux-cs.md) em seu dispositivo IOT ou nova VM.

1. Siga as instruções para [trigger_events](https://aka.ms/iot-security-github-trigger-events) executar um evento de linha de base do sistema operacional.

1. Verifique se as recomendações do defender for IoT em resposta à verificação da linha de base do sistema operacional simulado falha na etapa anterior. Comece a verificação 30 minutos após a execução do script.

## <a name="next-steps"></a>Próximas etapas

Configurar sua [solução](quickstart-configure-your-solution.md) 
 [criar módulos de segurança](quickstart-create-security-twin.md) configurar [alertas personalizados](quickstart-create-custom-alerts.md) 
 [implantar um agente de segurança](how-to-deploy-agent.md)

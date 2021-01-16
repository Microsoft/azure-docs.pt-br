---
title: Visão geral do agente de segurança
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
ms.date: 12/27/2019
ms.author: shhazam
ms.openlocfilehash: 2b1cd131e578b1d16fabee99b8de536e4a48ece0
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247294"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Introdução aos agentes de segurança de dispositivo do Azure defender para IoT

Os agentes de segurança do defender for IoT oferecem recursos de segurança aprimorados, como monitoramento de conexões remotas, aplicativos ativos, eventos de logon e práticas recomendadas de configuração do sistema operacional. Assuma o controle do seu campo de dispositivo proteção contra ameaças e postura de segurança com um único serviço.

A arquitetura de referência para agentes de segurança do Linux e do Windows, tanto em C# quanto em C, são fornecidas.

Os agentes de segurança do defender for IoT lidam com a coleta de eventos brutos do sistema operacional do dispositivo, agregação de eventos para reduzir o custo e a configuração por meio de um módulo de dispositivo. As mensagens de segurança são enviadas por meio do Hub IoT para o defender para serviços de análise de IoT.

Use o seguinte fluxo de trabalho para implantar e testar seus agentes de segurança do defender para IoT:

1. [Habilitar o defender para o serviço de IoT para o Hub IoT](quickstart-onboard-iot-hub.md)
1. Se o Hub IoT não tiver nenhum dispositivo registrado, [registre um novo dispositivo](../iot-accelerators/iot-accelerators-device-simulation-overview.md).
1. [Crie um módulo de segurança do azureiotsecurity](quickstart-create-security-twin.md) para seus dispositivos.
1. Para instalar o agente em um dispositivo simulado do Azure em vez de instalar em um dispositivo real, [crie uma nova VM (máquina virtual) do Azure](../virtual-machines/linux/quick-create-portal.md) em uma zona disponível.
1. [Implante um agente de segurança do defender para IOT](how-to-deploy-linux-cs.md) em seu dispositivo IOT ou nova VM.
1. Siga as instruções para [trigger_events](https://aka.ms/iot-security-github-trigger-events) executar uma simulação inofensiva de um ataque.
1. Verifique os alertas do defender para IoT em resposta ao ataque simulado na etapa anterior. Inicie a verificação cinco minutos depois de executar o script.
1. Explore [alertas](concept-security-alerts.md), [recomendações](concept-recommendations.md)e aprofunde-se [usando log Analytics](how-to-security-data-access.md) usando o Hub IOT.

## <a name="next-steps"></a>Próximas etapas

- Configurar sua [solução](quickstart-configure-your-solution.md)
- [Criar Módulos de Segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implantar um agente de segurança](how-to-deploy-agent.md)
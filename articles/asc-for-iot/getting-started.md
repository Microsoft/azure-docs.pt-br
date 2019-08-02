---
title: Começar a usar a Central de segurança do Azure (ASC) para a versão prévia do IoT | Microsoft Docs
description: Começar a entender o fluxo de trabalho básico da Central de segurança do Azure para recursos de IoT e o serviço.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 39f448f258923d23bdcba75a0a1987777ce0492e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616856"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Introdução à Central de segurança do Azure para IoT 

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação sobre os diferentes blocos de construção de Central de segurança do Azure (ASC) para o serviço de IoT e explica como começar com [habilitando o serviço](quickstart-onboard-iot-hub.md). 

ASC para IoT pode ser integrado perfeitamente em seu IoT Hub para fornecer padrões de comunicação de análise de segurança de configuração do hub IoT, identidade do dispositivo e do dispositivo do hub.
Para recursos de segurança avançada, ASC para IoT fornece com base em agente de coleta de dados de segurança de seus dispositivos IoT.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>ASC para integração perfeita de IoT Hub IoT

Ao tentar proteger os dispositivos IoT individuais, a capacidade de coletar dados diretamente dos dispositivos, ou de sua rede é necessária. Para dar suporte a esse esforço, ASC para IoT oferece um arsenal de agentes com poucos requisitos de segurança para fornecer monitoramento de dispositivo e sistema de proteção.

No ASC para versão prévia do IoT, arquitetura de referência para Linux e Windows agentes de segurança, tanto no C# e C são fornecidos.
Os agentes de lidar com a coleta de eventos brutos do sistema operacional do dispositivo, a agregação de eventos para reduzir o custo e a configuração por meio de um dispositivo gêmeo de módulo.
Mensagens de segurança são enviadas por meio do IoT Hub, para o ASC para serviços de análise de IoT.

## <a name="asc-for-iot-basics"></a>ASC para Noções básicas de IoT

Escolha o cenário de fluxo de trabalho que melhor atenda às suas necessidades de ambientes e dispositivos IoT:

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Introdução ao ASC para integração perfeita de IoT Hub IoT 

>[!Note]
>Esse fluxo de trabalho permite que você use o serviço sem usar ASC para agentes de segurança de IoT. 

Para habilitar o monitoramento de seu dispositivo gerenciamento de identidade, dispositivo de nuvem e nuvem para padrões de comunicação do dispositivo, utilize o fluxo de trabalho básico para teste e para iniciar o serviço: 

1. [Habilitar ASC para o serviço de IoT em seu IoT Hub](quickstart-onboard-iot-hub.md)
1. Se o seu IoT Hub não tem nenhum dispositivo registrado, [registrar um novo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Criar um módulo de segurança para seus dispositivos azureiotsecurity](quickstart-create-security-twin.md) para seus dispositivos. 
1. Definir o comportamento normal do sistema e de dispositivo por meio [alertas personalizados](quickstart-create-custom-alerts.md). 
1. Execute os testes para verificar o status do serviço e dispositivo do sistema. 
1. Explore [alertas](concept-security-alerts.md), [recomendações](concept-recommendations.md), e [usando o Log Analytics de aprofundamento](how-to-security-data-access.md) usando o IoT Hub. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Introdução ao ASC para agentes de segurança de IoT

Certifique-se de usar ASC para recursos de segurança aprimorada de IoT, como o monitoramento de conexões remotas, aplicativos ativos, eventos de logon e práticas recomendadas de configuração do sistema operacional usando o seguinte fluxo de trabalho básico para testar e habilitar o serviço: 

1. [Habilitar ASC para o serviço de IoT ao IoT Hub](quickstart-onboard-iot-hub.md)
1. Se o seu IoT Hub não tem nenhum dispositivo registrado, [registrar um novo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Criar um módulo de segurança azureiotsecurity](quickstart-create-security-twin.md) para seus dispositivos.
1. Para instalar o agente em um dispositivo simulado do Azure em vez de instalar em um dispositivo real, [backup de um novo Azure VM (Máquina Virtual) de rotação](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) em uma zona disponível. 
1. [Implantar um ASC para agente de segurança de IoT](how-to-deploy-linux-cs.md) em seu dispositivo IoT ou nova VM.
1. Siga as instruções para [trigger_events](https://aka.ms/iot-security-github-trigger-events) para executar uma simulação de um ataque inofensivo.
1. Verifique se ASC para alertas de IoT em resposta ao ataque simulado na etapa anterior. Iniciar verificação de cinco minutos após a execução do script.
1. Explore [alertas](concept-security-alerts.md), [recomendações](concept-recommendations.md), e [usando o Log Analytics de aprofundamento](how-to-security-data-access.md) usando o IoT Hub. 

## <a name="next-steps"></a>Próximas etapas

- Habilitar [ASC para IoT](quickstart-onboard-iot-hub.md)
- Configurar seu [solução](quickstart-configure-your-solution.md)
- [Criar Módulos de Segurança](quickstart-create-security-twin.md)
- Configurar [alertas personalizados](quickstart-create-custom-alerts.md)
- [Implantar um agente de segurança](how-to-deploy-agent.md)

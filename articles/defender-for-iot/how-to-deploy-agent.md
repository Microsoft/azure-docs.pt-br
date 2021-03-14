---
title: Selecionar e implantar agentes de segurança
description: Saiba mais sobre como selecionar e implantar agentes de segurança do defender para IoT em dispositivos IoT.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 89ecf26778433a3f075f76905183f3fe866cb5db
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447258"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selecionar e implantar um agente de segurança em seu dispositivo IoT

O defender para IoT fornece arquiteturas de referência para agentes de segurança que monitoram e coletam dados de dispositivos IoT.
Para saber mais, consulte [arquitetura de referência do agente de segurança](security-agent-architecture.md).

Os agentes são desenvolvidos como projetos de código-fonte aberto e estão disponíveis em dois tipos: <br> [C](https://aka.ms/iot-security-github-c)e [C#](https://aka.ms/iot-security-github-cs).

Neste artigo, você aprenderá como:
- Comparar tipos de agente de segurança
- Descobrir plataformas de agente com suporte
- Escolha o tipo de agente correto para sua solução

## <a name="understand-security-agent-options"></a>Entender as opções do agente de segurança

Cada tipo de agente de segurança do defender para IoT oferece o mesmo conjunto de recursos e oferece suporte a opções de configuração semelhantes.

O agente de segurança baseado em C tem uma superfície de memória menor e é a opção ideal para dispositivos com menos recursos disponíveis.

|     | Agente de segurança baseado em C | Agente de segurança baseado em C# |
| --- | ----------- | --------- |
| **Software livre** | Disponível sob [licença do MIT](https://en.wikipedia.org/wiki/MIT_License) no [GitHub](https://aka.ms/iot-security-github-c) | Disponível sob [licença do MIT](https://en.wikipedia.org/wiki/MIT_License) no [GitHub](https://aka.ms/iot-security-github-cs) |
| **Linguagem de desenvolvimento**    | C | C# |
| **Plataformas Windows com suporte?** | Não | Sim |
| **Pré-requisitos do Windows** | --- | [WMI](/windows/desktop/wmisdk/) |
| **Plataformas Linux com suporte?** | Sim, x64 e x86 | Sim, somente x64 |
| **Pré-requisitos do Linux** | libunwind8, libcurl3, UUID-Runtime, audited, audispd-plugins | libunwind8, libcurl3, UUID-Runtime, audited, audispd-plugins, sudo, netstat, iptables |
| **Espaço em disco** | 10,5 MB | 90 MB |
| **Superfície de memória (em média)** | 5,5 MB | 33 MB |
| **[Autenticação](concept-security-agent-authentication-methods.md) para o Hub IOT** | Sim | Sim |
| **[Coleta](how-to-agent-configuration.md#supported-security-events) de dados de segurança** | Sim | Sim |
| **Agregação de eventos** | Sim | Sim |
| **Configuração remota por meio do [módulo de segurança](concept-security-module.md) ...** | Sim | Sim |

## <a name="security-agent-installation-guidelines"></a>Diretrizes de instalação do agente de segurança

Para **Windows**: o script de instalação SecurityAgent.ps1 deve ser executado a partir de uma janela de administrador do PowerShell.

Para **Linux**: o InstallSecurityAgent.sh deve ser executado como superusuário. É recomendável prefixar o comando de instalação com "sudo".

## <a name="choose-an-agent-flavor"></a>Escolher um tipo de agente

Responda às seguintes perguntas sobre seus dispositivos IoT para selecionar o agente correto:

- Você está usando o _Windows Server_ ou o _Windows IOT Core_?

    [Implante um agente de segurança baseado em C# para Windows](how-to-deploy-windows-cs.md).

- Você está usando uma distribuição do Linux com arquitetura x86?

    [Implante um agente de segurança baseado em C para Linux](how-to-deploy-linux-c.md).

- Você está usando uma distribuição do Linux com arquitetura x64?

    Ambos os tipos de agente podem ser usados. <br>
    [Implante um agente de segurança baseado em C para Linux](how-to-deploy-linux-c.md) e/ou [implante um agente de segurança baseado em C# para Linux](how-to-deploy-linux-cs.md).

Ambos os tipos de agente oferecem o mesmo conjunto de recursos e oferecem suporte a opções de configuração semelhantes.
Consulte [comparação do agente de segurança](how-to-deploy-agent.md#understand-security-agent-options) para saber mais.

## <a name="supported-platforms"></a>Plataformas compatíveis

A lista a seguir inclui todas as plataformas com suporte no momento.

|Agente do defender para IoT |Sistema operacional |Arquitetura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, build 17763    |x64|
|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções de configuração, prossiga para o guia de instruções para a configuração do agente.
> [!div class="nextstepaction"]
> [Guia de instruções de configuração do agente](./how-to-agent-configuration.md)
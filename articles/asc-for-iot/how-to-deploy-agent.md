---
title: Selecione e implante a Central de segurança do Azure para visualização do agente de IoT | Microsoft Docs
description: Saiba mais sobre como selecionar e implantar a Central de segurança do Azure para os agentes de segurança de IoT nos dispositivos de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: c549e5ccbda9b364b3e7d20c9572eb777c32299e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616824"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selecione e implantar um agente de segurança em seu dispositivo IoT

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Central de segurança (ASC) do Azure para IoT fornece arquiteturas de referência para os agentes de segurança que monitoram e coletam dados de dispositivos IoT.
Ver [arquitetura de referência do agente de segurança](security-agent-architecture.md) para saber mais.

Os agentes são desenvolvidos como projetos de software livre e estão disponíveis em duas versões: <br> [C](https://aka.ms/iot-security-github-c), e [ C# ](https://aka.ms/iot-security-github-cs).

Neste artigo, você aprenderá a: 
> [!div class="checklist"]
> * Comparar tipos de agente de segurança
> * Descubra as plataformas com suporte do agente
> * Escolha o tipo de agente correto para sua solução

## <a name="understand-security-agent-options"></a>Entender as opções de segurança do agente

Cada ASC para sabor de agente de segurança de IoT oferece o mesmo conjunto de recursos e dá suporte a opções de configuração semelhante. 

O agente de segurança com base em C tem uma menor superfície de memória e é a opção ideal para dispositivos com poucos recursos disponíveis. 

|     | Agente de segurança com base em C | C#-com base em agente de segurança |
| --- | ----------- | --------- |
| Código-fonte aberto | Disponível em [licença MIT](https://en.wikipedia.org/wiki/MIT_License) em [Github](https://aka.ms/iot-security-github-cs) | Disponível em [licença MIT](https://en.wikipedia.org/wiki/MIT_License) em [Github](https://aka.ms/iot-security-github-c) |
| Linguagem de desenvolvimento    | C | C# |
| Plataformas com suporte do Windows? | Não | Sim |
| Pré-requisitos do Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Plataformas com suporte do Linux? | Sim, x64 e x86 | Sim, somente x64 |
| Pré-requisitos do Linux | libunwind8, libcurl3, tempo de execução de uuid, auditd, plug-ins de audispd | libunwind8, libcurl3, tempo de execução de uuid, auditd, plug-ins de audispd, sudo, netstat, iptables |
| Superfície de disco | 10.5 MB | 90MB |
| Volume de memória (em média) | 5.5 MB | 33MB |
| [Autenticação](concept-security-agent-authentication-methods.md) ao Hub IoT | Sim | Sim |
| Dados de segurança [coleção](how-to-agent-configuration.md#supported-security-events) | Sim | Sim |
| Agregação de eventos | Sim | Sim |
| Configuração remota por meio de [gêmeo de módulo de segurança](concept-security-module.md) | Sim | Sim |


## <a name="choose-an-agent-flavor"></a>Escolha um tipo de agente 

Responda às seguintes perguntas sobre os dispositivos de IoT para selecionar o agente correto:

- Você está usando _Windows Server_ ou _Windows IoT Core_? 

    [Implantar um C#-com base em agente de segurança para Windows](how-to-deploy-windows-cs.md).

- Você está usando uma distribuição Linux com x86 arquitetura? 

    [Implantar um agente de segurança com base em C para Linux](how-to-deploy-linux-c.md).

- Você está usando uma distribuição Linux com x64 arquitetura?

    Você pode usar qualquer tipo de agente. <br>
    [Implantar um agente de segurança com base em C para Linux](how-to-deploy-linux-c.md) e/ou [implantar um C#-com base em agente de segurança para Linux](how-to-deploy-linux-cs.md).

Ambos os tipos de agente oferecem o mesmo conjunto de recursos e opções de configuração semelhante de suporte.
Ver [comparação de agente de segurança](how-to-deploy-agent.md#understand-security-agent-options) para saber mais.

## <a name="supported-platforms"></a>Plataformas com suporte

A lista a seguir inclui todas as plataformas com suporte no momento.

|ASC para agente do IoT |Sistema operacional |Arquitetura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   X64|
|C|Ubuntu 18.04 |   X64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |X64|
|C#|Ubuntu 18.04    |X64|
|C#|Debian 9    |X64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core build 17763 |X64|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre opções de configuração, continue para o guia de instruções para configuração do agente. 
> [!div class="nextstepaction"]
> [Configuração do agente de guia de instruções](./how-to-agent-configuration.md)

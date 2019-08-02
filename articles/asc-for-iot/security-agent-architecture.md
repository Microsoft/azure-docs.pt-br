---
title: Noções básicas sobre Central de segurança do Azure para arquitetura de agente de segurança de IoT Preview | Microsoft Docs
description: Entenda a arquitetura de segurança do agente para os agentes usados na Central de segurança do Azure para o serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f4ca9a2689ab9e09b4ebff903e757f5c352b556
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616574"
---
# <a name="security-agent-reference-architecture"></a>Arquitetura de referência do agente de segurança

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Central de segurança (ASC) do Azure para IoT fornece a arquitetura de referência para os agentes de segurança que faça logon, processam, agregam e enviam dados de segurança por meio do hub IoT.

Agentes de segurança são projetados para funcionar em um ambiente restrito de IoT e são altamente personalizáveis em termos de valores que eles fornecem em comparação com os recursos que eles consomem.

Agentes de segurança suportam os seguintes recursos:

- Colete eventos de segurança brutas do sistema operacional subjacente (Linux, Windows). Para saber mais sobre os coletores de dados de segurança disponíveis, consulte [ASC para configuração do agente IoT](how-to-agent-configuration.md).

- Agregar os eventos de segurança brutos em mensagens enviadas por meio do hub IoT.

- Autenticar com a identidade do dispositivo existente ou uma identidade de módulo dedicado. Ver [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md) para saber mais.

- Configurar remotamente por meio do uso do **azureiotsecurity** gêmeo do módulo. Para obter mais informações, consulte [configurar um ASC para agente do IoT](how-to-agent-configuration.md).

ASC para agentes de segurança de IoT são desenvolvidas como projetos de código-fonte aberto e estão disponível no GitHub: 

- [ASC para agente de C do IoT](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [ASC para IoT C#-com base em agente](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Plataformas com suporte do agente

ASC para IoT oferece agentes diferentes do instalador de 32 bits e Windows de 64 bits e o mesmo para 32 bits e 64 bits Linux. Verifique se que você tem o instalador do agente correto para cada um dos seus dispositivos de acordo com a tabela a seguir:

| 32 ou 64 bits | Linux | Windows |    Detalhes|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 bits  | C  | C#  ||
| 64 bits  | C#ou C           | C#      | Use o agente de C para dispositivos com o mínimo de recursos|

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre ASC para arquitetura de agente de segurança de IoT e os instaladores disponíveis.

Para continuar a introdução ao ASC para implantação de IoT, use os seguintes artigos:

- Entender [métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)
- Selecione e implante um [agente de segurança](how-to-deploy-agent.md)
- Examine o ASC para IoT [pré-requisitos de serviço](service-prerequisites.md)
- Saiba como [ASC habilitar para o serviço de IoT em seu IoT Hub](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [ASC para perguntas frequentes sobre o IoT](resources-frequently-asked-questions.md)

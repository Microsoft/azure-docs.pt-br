---
title: Introdução à automação no Azure Sentinel | Microsoft Docs
description: Este artigo apresenta os recursos de orquestração de segurança, automação e resposta (disparar) do Azure Sentinel e descreve seus componentes do disparar – regras de automação e guias estratégicos.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609023"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Orquestração de segurança, automação e resposta (disparar) no Azure Sentinel

Este artigo descreve os recursos de orquestração de segurança, automação e resposta (disparar) do Azure Sentinel e mostra como o uso de regras de automação e guias estratégicos em resposta a ameaças de segurança aumenta a eficácia do SOC e poupa tempo e recursos.

## <a name="azure-sentinel-as-a-soar-solution"></a>Azure Sentinel como uma solução disparar

### <a name="the-problem"></a>O problema

As equipes SIEM/SOC normalmente são inundadas com alertas de segurança e incidentes regularmente, em volumes tão grandes que a equipe disponível está sobrecarregada. Isso resulta muito frequentemente em situações em que muitos alertas são ignorados e muitos incidentes não são investigados, deixando a organização vulnerável a ataques que passam despercebidos.

### <a name="the-solution"></a>A solução

O Azure Sentinel, além de ser um sistema SIEM (gerenciamento de informações e eventos de segurança), também é uma plataforma para orquestração de segurança, automação e resposta (disparar). Uma das suas principais finalidades é automatizar qualquer tarefa recorrente e previsível, de reversão e de remediação, que seja responsabilidade do seu centro de operações de segurança e pessoal (SOC/SecOps), liberando tempo e recursos para uma investigação mais aprofundada e procurando ameaças avançadas. A automação usa algumas formas diferentes no Azure Sentinel, de regras de automação que gerenciam centralmente a automação de tratamento e resposta de incidentes, para os guias estratégicos que executam sequências de ações predeterminadas para fornecer automação avançada e flexível para suas tarefas de resposta a ameaças.

## <a name="automation-rules"></a>Regras de automação

As regras de automação são um novo conceito no Azure Sentinel. Esse recurso permite que os usuários gerenciem centralmente a automação da manipulação de incidentes. Além de permitir que você atribua guias estratégicos a incidentes (não apenas para alertas como antes), as regras de automação também permitem automatizar as respostas para várias regras de análise de uma vez, marcar automaticamente, atribuir ou fechar incidentes sem a necessidade de guias estratégicos e controlar a ordem das ações executadas. As regras de automação simplificarão o uso da automação no Azure Sentinel e permitirão que você simplifique fluxos de trabalho complexos para seus processos de orquestração de incidentes.

Saiba mais com esta [explicação completa de regras de automação](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - O recurso de **regras de automação** está atualmente em **Visualização**. Consulte os [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) de termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, visualização ou, de outra forma, ainda não foram lançadas em disponibilidade geral.

## <a name="playbooks"></a>Guias estratégicos

Um guia estratégico é uma coleção de ações de resposta e correção e lógica que pode ser executada do Azure Sentinel como uma rotina. Um guia estratégico pode ajudar a automatizar e orquestrar sua resposta a ameaças, pode ser integrado a outros sistemas internos e externos, e pode ser definido para ser executado automaticamente em resposta a alertas ou incidentes específicos, quando disparado por uma regra de análise ou uma regra de automação, respectivamente. Ele também pode ser executado manualmente sob demanda, em resposta a alertas, na página incidentes.

Os guias estratégicos no Azure Sentinel se baseiam em fluxos de trabalho criados em [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), um serviço de nuvem que ajuda a agendar, automatizar e orquestrar tarefas e fluxos de trabalho entre sistemas em toda a empresa. Isso significa que os guias estratégicos podem aproveitar toda a capacidade e a personalização dos recursos de integração e orquestração dos aplicativos lógicos e ferramentas de design fáceis de usar, bem como escalabilidade, confiabilidade e nível de serviço de um serviço do Azure de camada 1.

Saiba mais com esta [explicação completa dos guias estratégicos](automate-responses-with-playbooks.md).

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como o Azure Sentinel usa a automação para ajudar seu SOC a operar de forma mais eficiente e eficiente.

- Para saber mais sobre a automação da manipulação de incidentes, consulte [automatizar o tratamento de incidentes no Azure Sentinel](automate-incident-handling-with-automation-rules.md).
- Para saber mais sobre opções de automação avançadas, confira [automatizar a resposta contra ameaças com guias estratégicos no Azure Sentinel](automate-responses-with-playbooks.md).
- Para obter ajuda na implementação de regras de automação e guias estratégicos, consulte [tutorial: usar guias estratégicos para automatizar respostas de ameaças no Azure Sentinel](tutorial-respond-threats-playbook.md).

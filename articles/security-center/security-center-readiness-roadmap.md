---
title: Roteiro de preparação da Central de Segurança do Azure | Microsoft Docs
description: Este documento fornece um roteiro de preparação para incrementar a Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: e4f905b7a08564eadb20d65d651c137ef3741376
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100314"
---
# <a name="azure-security-center-readiness-roadmap"></a>Roteiro de preparação da central de segurança do Azure
Este documento fornece um roteiro de preparação que ajudará você a começar a usar a Central de Segurança do Azure.

## <a name="understanding-security-center"></a>Noções básicas sobre a Central de Segurança
A Central de Segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, localmente e em outras nuvens. 

Use os seguintes recursos para começar com a Central de Segurança.

Artigos
- [Introdução à Central de Segurança do Azure](security-center-introduction.md)
- [Guia de início rápido da Central de Segurança do Azure](security-center-get-started.md)

vídeos
- [Vídeo de introdução rápida](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Visão geral dos recursos de resposta, de detecção e de prevenção da Central de Segurança](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planejamento e operações

Para tirar total proveito da Central de Segurança, é importante entender como as pessoas ou equipes diferentes em sua organização usam o serviço para atender às necessidades de operações seguras, monitoramento, administração resposta a incidentes.

Use os recursos a seguir para ajudá-lo durante os processos de planejamento e de operações.

- [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Integração de computadores à Central de Segurança
A central de segurança detecta automaticamente quaisquer assinaturas ou espaços de trabalho do Azure não protegidos pelo Azure defender. Isso inclui assinaturas do Azure usando a central de segurança gratuita e espaços de trabalho que não têm a solução de segurança habilitada.

Use os recursos a seguir para ajudá-lo durante os processos de integração.

- [Integrar computadores não Azure](quickstart-onboard-machines.md)
- [Central de Segurança do Azure híbrido - visão geral](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Como eliminar problemas de segurança usando a Central de Segurança
A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros conectados, como firewall e soluções de proteção de ponto de extremidade, a fim de detectar ameaças reais e reduzir os falsos positivos.

Use os recursos a seguir para ajudá-lo a gerenciar alertas de segurança e a proteger seus recursos.

Artigos    
- [Protegendo sua rede na Central de Segurança do Azure](./security-center-network-recommendations.md)
- [Proteção dos dados e do serviço SQL do Azure na Central de Segurança do Azure](./security-center-remediate-recommendations.md)


Vídeo    
- [Como eliminar problemas de segurança usando a Central de Segurança do Azure](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Central de Segurança para resposta a incidentes
Para reduzir os custos e danos, é importante ter um plano de resposta a incidentes em vigor antes que um ataque ocorra. A Central de Segurança do Azure pode ser usada em diferentes estágios de uma resposta a incidentes.

Use os recursos a seguir para entender como a Central de Segurança pode ser incorporada ao processo de resposta a incidentes.

vídeos    
* [Central de Segurança do Azure em resposta a incidentes](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Responder rapidamente às ameaças com a operação de segurança de última geração e a investigação](https://youtu.be/e8iFCz5RM4g)

Artigos    
* [Uso da Central de Segurança do Azure para uma resposta a incidentes](./tutorial-security-incident.md)
* [Usar a automação para responder aos gatilhos da central de segurança](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Defesa da nuvem avançada

As Máquinas Virtuais do Azure podem tirar proveito dos recursos avançados de defesa de nuvem na Central de Segurança. Esses recursos incluem o acesso just-in-time à VM (máquina virtual) e os controles de aplicativos adaptáveis.

Use os seguintes recursos para saber como usar esses recursos na Central de Segurança.

vídeos    
* [Central de Segurança do Azure – Acesso de VM just-in-time](https://youtu.be/UOQb2FcdQnU)
* [Central de Segurança do Azure: Controles de Aplicativo Adaptáveis](https://youtu.be/wWWekI1Y9ck)

Artigos    
* [Gerenciar o acesso à máquina virtual com just-in-time](./security-center-just-in-time.md)
* [Controles de aplicativo adaptáveis na Central de Segurança do Azure](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>Atividades práticas

* [Laboratório prático da Central de Segurança](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Guia estratégico de recomendação do WAF (Firewall do Aplicativo Web) na Central de Segurança](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Guia estratégico da Central de Segurança do Azure: Alertas de segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Recursos adicionais
* [Página de documentação da Central de Segurança](./index.yml)
* [Página de documentação da API REST da Central de Segurança](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Perguntas frequentes sobre a Central de Segurança do Azure](./faq-general.md)
* [Página de preços da Central de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/)
* [Práticas recomendadas de segurança de identidade](../security/fundamentals/identity-management-best-practices.md)
* [Práticas recomendadas de segurança da rede](../security/fundamentals/network-best-practices.md)
* [Recomendações de PaaS](../security/fundamentals/paas-deployments.md)
* [Conformidade](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Os clientes do Log Analytics agora podem usar a Central de Segurança do Azure para proteger suas cargas de trabalho de nuvem híbrida](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>Recursos da comunidade

* [UserVoice da Central de Segurança](https://feedback.azure.com/forums/347535-azure-security-center)
* [P&uma página para A central de segurança](/answers/topics/azure-security-center.html)
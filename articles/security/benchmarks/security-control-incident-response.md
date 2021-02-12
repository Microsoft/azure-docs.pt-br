---
title: Controle de segurança do Azure-resposta a incidentes
description: Resposta a incidentes de controle de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fb4c4c5a0cf6610af17aabc562c42d2e0eb4e6a4
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409086"
---
# <a name="security-control-incident-response"></a>Controle de segurança: resposta a incidentes

Proteja as informações da organização, bem como sua reputação, desenvolvendo e implementando uma infraestrutura de resposta a incidentes (por exemplo, planos, funções definidas, treinamento, comunicações, supervisão de gerenciamento) para detectar rapidamente um ataque e, em seguida, conter efetivamente os danos, erradicar a presença do invasor e restaurar a integridade da rede e dos sistemas.

## <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Cliente |

Crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.  

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10,2 | 19,8 | Cliente |

A central de segurança atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta. 

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

## <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10,3 | 19 | Cliente |

Conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10.4 | 19,5 | Cliente |

As informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma parte ilegal ou não autorizada. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10.5 | 19,6 | Cliente |

Exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 10,6 | 19 | Cliente |

Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../../security-center/workflow-automation.md)


## <a name="next-steps"></a>Próximas etapas

- Veja o próximo controle de segurança: [testes de penetração e exercícios de equipe vermelhos](security-control-penetration-tests-red-team-exercises.md)
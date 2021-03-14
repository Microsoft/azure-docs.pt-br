---
title: Benchmark de segurança do Azure v2-governança e estratégia
description: Estratégia e governança de benchmark de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 277033e41ec7e02b89eca8cf74fe6854acb51cc1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727015"
---
# <a name="security-control-v2-governance-and-strategy"></a>Controle de segurança v2: governança e estratégia

Governança e estratégia fornece orientação para garantir uma estratégia de segurança coerente e uma abordagem de governança documentada para orientar e sustentar a garantia de segurança, incluindo o estabelecimento de funções e responsabilidades para as diferentes funções de segurança de nuvem, estratégia técnica unificada e políticas e padrões de suporte.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definir a estratégia de proteção de dados e gerenciamento de ativos

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Certifique-se de documentar e comunicar uma estratégia clara para o monitoramento contínuo e a proteção de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o monitoramento de sistemas e dados comercialmente críticos.

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

- Padrão de classificação de dados de acordo com os riscos de negócios

- Visibilidade da organização de segurança dos riscos e do inventário de ativos

- Aprovação da organização de segurança dos serviços do Azure para uso

- Segurança de ativos por meio do ciclo de vida

- Estratégia de controle de acesso obrigatório de acordo com a classificação de dados organizacionais

- Uso de funcionalidades nativas e de terceiros de proteção de dados do Azure

- Requisitos de criptografia de dados para casos de uso em trânsito e em repouso

- Padrões de criptografia apropriados

Para saber mais, consulte as referências a seguir:
- [Recomendação da arquitetura de segurança do Azure – Armazenamento, dados e criptografia](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Conceitos básicos de segurança do Azure – Segurança, criptografia e armazenamento de dados do Azure](../fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Melhores práticas de segurança e criptografia de dados do Azure](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – Gerenciamento de ativos](security-controls-v2-asset-management.md)

- [Azure Security Benchmark – Proteção de dados](security-controls-v2-data-protection.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definir uma estratégia de segmentação corporativa

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Estabeleça uma estratégia de toda a empresa para segmentar o acesso aos ativos usando uma combinação de identidade, rede, aplicativo, assinatura, grupo de gerenciamento e outros controles.

Equilibre cuidadosamente a necessidade de separação de segurança com a necessidade de habilitação da operação diária dos sistemas que precisam se comunicar entre si e acessar dados.

Garanta que a estratégia de segmentação seja implementada de maneira consistente nos tipos de controle, incluindo segurança de rede, modelos de identidade e acesso, além de modelos de acesso/permissão de aplicativo e controles de processos humanos.

- [Diretrizes sobre a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Diretrizes sobre a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação corporativa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definir uma estratégia de gerenciamento de postura de segurança

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Meça e reduza continuamente os riscos para seus ativos individuais e o ambiente no qual eles estão hospedados. Priorize os ativos de alto valor e as superfícies de ataque altamente expostas, como aplicativos publicados, pontos de entrada e saída de rede, pontos de extremidade de usuário e administrador etc.

- [Azure Security Benchmark – Gerenciamento de postura e vulnerabilidade](security-controls-v2-posture-vulnerability-management.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: alinhar funções e responsabilidades da organização

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| GS-4 | N/D | PL, PM |

Certifique-se de documentar e comunicar uma estratégia clara para funções e responsabilidades em sua organização de segurança. Priorize o fornecimento de responsabilidade clara por decisões de segurança, educando todos no modelo de responsabilidade compartilhada e instrua equipes técnicas sobre a tecnologia usada para proteger a nuvem.

- [Melhor prática de segurança do Azure 1 – Pessoas: educar as equipes na jornada de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor prática de segurança do Azure 2 – Pessoas: educar as equipes na tecnologia de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor prática de segurança do Azure 3 – Processo: atribuir responsabilidade por decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: definir uma estratégia de segurança de rede

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| GS-5 | 9 | CA, SC |

Estabeleça uma abordagem de segurança de rede do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos:

- Gerenciamento de rede centralizado e responsabilidade pela segurança

- Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação corporativa

- Estratégia de correção em diferentes cenários de ameaças e ataques

- Estratégia de entrada e saída e borda da Internet

- Estratégia de interconectividade local e de nuvem híbrida

- Artefatos de segurança de rede atualizados (como diagramas de rede, arquitetura de rede de referência)

Para saber mais, consulte as referências a seguir:

- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Segurança de rede](security-controls-v2-network-security.md)

- [Visão geral da segurança de rede do Azure](../fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede corporativa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definir uma estratégia de identidade e acesso privilegiado

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Estabeleça uma identidade do Azure e abordagens de acesso privilegiado como parte da estratégia geral de controle de acesso de segurança da sua organização.

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos:

- Um sistema de identidade e autenticação centralizado e a interconectividade dele com outros sistemas de identidade internos e externos

- Métodos de autenticação forte em diferentes casos de uso e condições

- Proteção de usuários altamente privilegiados

- Monitoramento e tratamento de atividades de usuário de anomalias

- Processo de revisão de acesso e reconciliação e identidade do usuário

Para saber mais, consulte as referências a seguir:

- [Azure Security Benchmark – Gerenciamento de identidades](security-controls-v2-identity-management.md)

- [Azure Security Benchmark – Acesso privilegiado](security-controls-v2-privileged-access.md)

- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança de gerenciamento de identidade do Azure](../fundamentals/identity-management-overview.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definir uma estratégia de log e resposta a ameaças

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Estabeleça uma estratégia de resposta a ameaças e registro para detectar e corrigir ameaças rapidamente, atendendo aos requisitos de conformidade. Priorize o fornecimento de alertas de alta qualidade e experiências integradas aos analistas, de modo que possam se concentrar nas ameaças em vez da integração e de etapas manuais. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

- A função e as responsabilidades da organização de operações de segurança (SecOps) 

- Um processo de resposta a incidentes bem definido, alinhando-se ao NIST ou a outra estrutura do setor 

- Captura e retenção de log para dar suporte às necessidades de conformidade, detecção de ameaças e resposta a incidentes

- Visibilidade centralizada das ameaças e informações de correlação sobre elas por meio do SIEM, de funcionalidades nativas do Azure e de outras fontes 

- Plano de comunicação e notificação com seus clientes, fornecedores e partes públicas interessadas

- Uso de plataformas nativas e de terceiros do Azure para tratamento de incidentes, como log e detecção de ameaças, análise forense, além de correção e erradicação de ataques

- Processos para tratamento de incidentes e atividades pós-incidente, como lições aprendidas e retenção de evidências

Para saber mais, consulte as referências a seguir:
- [Azure Security Benchmark – Log e detecção de ameaças](security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Resposta a incidentes](security-controls-v2-incident-response.md)

- [Melhor prática de segurança do Azure 4 – Processo: atualizar processos de resposta a incidentes para a nuvem](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Guia de log, de decisão de relatórios e do Adoption Framework do Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala empresarial, gerenciamento e monitoramento do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: definir a estratégia de backup e recuperação

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| GS-8 | 10 | CP |

Estabeleça uma estratégia de backup e recuperação do Azure para sua organização. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

- As definições de RTO (objetivo de tempo de recuperação) e RPO (objetivo de ponto de recuperação) de acordo com seus objetivos de resiliência de negócios

- Design de redundância em sua configuração de infraestrutura e aplicativos

- Proteção de backup usando controle de acesso e criptografia de dados

Para saber mais, consulte as referências a seguir:
- [Benchmark de segurança do Azure-backup e recuperação](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework-backup e recuperação de desastre para aplicativos do Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Estrutura de adoção do Azure-continuidade dos negócios e recuperação de desastre](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Todos os participantes](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)
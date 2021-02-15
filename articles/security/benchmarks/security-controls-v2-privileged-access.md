---
title: Benchmark de segurança do Azure v2-acesso privilegiado
description: Acesso privilegiado do benchmark de segurança do Azure v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd39f5e0af34c702cddc8e08b6a94e428c7f0167
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092043"
---
# <a name="security-control-v2-privileged-access"></a>Controle de segurança v2: acesso privilegiado

O acesso privilegiado abrange controles para proteger o acesso privilegiado ao seu locatário e recursos do Azure. Isso inclui uma variedade de controles para proteger seu modelo administrativo, contas administrativas e estações de trabalho de acesso privilegiado contra riscos deliberados e inadvertidos.

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: proteger e limitar os usuários altamente privilegiados

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Limite o número de contas de usuário altamente privilegiadas e proteja essas contas em um nível elevado. As funções internas mais críticas no Azure AD são o administrador global e o administrador de função com privilégios, pois os usuários atribuídos a essas duas funções podem delegar funções de administrador. Com esses privilégios, os usuários podem ler e modificar de forma direta ou indireta todos os recursos em seu ambiente do Azure:

- Administrador global: os usuários com essa função têm acesso a todos os recursos administrativos no Azure AD, bem como a serviços que usam identidades do Azure AD.

- Administrador de função com privilégios: os usuários com essa função podem gerenciar atribuições de função no Azure AD, bem como dentro de Azure AD Privileged Identity Management (PIM). Além disso, essa função permite o gerenciamento de todos os aspectos do PIM e das unidades administrativas.

Observação: você pode ter outras funções críticas que precisam ser governadas se você usar funções personalizadas com determinadas permissões privilegiadas atribuídas. E você também pode querer aplicar controles semelhantes à conta de administrador de ativos de negócios críticos.  

Você pode habilitar o acesso privilegiado JIT (just-in-time) aos recursos do Azure e ao Azure AD usando o Azure AD PIM (Privileged Identity Management). O JIT concede permissões temporárias para executar tarefas privilegiadas somente quando os usuários precisam dela. O PIM também pode gerar alertas de segurança quando há atividades suspeitas ou não seguras na sua organização do Azure AD.

- [Permissões da função Administrador no Azure AD](../../active-directory/roles/permissions-reference.md)

- [Usar os alertas de segurança do Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Protegendo o acesso privilegiado para implantações de nuvem e híbridos no Azure AD](../../active-directory/roles/security-planning.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| PA-2 | 13,2, 2,10 | AC-2, SC-3, SC-7 |

Isole o acesso a sistemas críticos para os negócios, restringindo quais contas recebem acesso privilegiado às assinaturas e aos grupos de gerenciamento em que estão. Certifique-se de também restringir o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo aos seus ativos críticos para os negócios, como controladores de Domínio do Active Directory (DCs), ferramentas de segurança e ferramentas de gerenciamento do sistema com agentes instalados em sistemas comerciais críticos. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem fazê-los imediatamente para comprometer os ativos críticos aos negócios. 

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente. 

Certifique-se de atribuir contas privilegiadas separadas que sejam diferentes das contas de usuário padrão usadas para tarefas de email, navegação e produtividade.

- [Componentes do Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao grupo de gerenciamento](../../governance/management-groups/overview.md#management-group-access)

- [Administradores de assinatura do Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: examinar e reconciliar regularmente o acesso do usuário

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| PA-3 | 4,1, 16,9, 16,10 | AC-2 |

Revise as contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seus níveis de acesso sejam válidos. Você pode usar as revisões de acesso do Azure AD para revisar as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas. Você também pode usar Azure AD Privileged Identity Management para criar um fluxo de trabalho de relatório de revisão de acesso que facilita o processo de revisão.
Além disso, o Azure Privileged Identity Management pode ser configurado para alertar quando um número excessivo de contas de administrador for criado e para identificar contas de administrador que estão obsoletas ou configuradas incorretamente. 

Observação: alguns serviços do Azure dão suporte a usuários e funções locais que não são gerenciados por meio do Azure AD. Você deve gerenciar esses usuários separadamente.

- [Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Como usar as revisões de identidade e acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurar o acesso de emergência no Azure AD

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

Para evitar que seja bloqueado acidentalmente da sua organização do Azure AD, configure uma conta de acesso de emergência para acesso quando contas administrativas normais não puderem ser usadas. As contas de acesso de emergência geralmente são altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas.
Você deve verificar se as credenciais (como senha, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas pelos indivíduos que têm autorização para usá-las somente em uma emergência.

- [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../../active-directory/roles/security-emergency-access.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operações de segurança (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizar o gerenciamento de direitos

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Use os recursos de gerenciamento de direitos do Azure AD para automatizar fluxos de trabalho de solicitação de acesso, incluindo atribuições de acesso, revisões e expiração. Também há suporte para a aprovação dupla ou de vários estágios.
- [O que são revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md) 

- [O que é o gerenciamento de direitos do AD do Azure](../../active-directory/governance/entitlement-management-overview.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| PA-6 | 4,6, 11,6, 12,12 | AC-2, SC-3, SC-7 |

Estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviço críticos. Use estações de trabalho de usuário altamente protegidas e/ou bastiões do Azure para tarefas administrativas. Use o Azure Active Directory, o Microsoft ATP (Proteção Avançada contra Ameaças) e/ou o Microsoft Intune para implantar uma estação de trabalho do usuário protegida e gerenciada para tarefas administrativas. As estações de trabalho protegidas podem ser gerenciadas centralmente para impor configuração segura, incluindo autenticação forte, linhas de base de software e hardware e acesso lógico restrito à rede. 

- [Entender as estações de trabalho com acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Implantar uma estação de trabalho com acesso privilegiado](/security/compass/privileged-access-deployment)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Operações de segurança (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguir a administração Just Enough (princípio de privilégios mínimos)

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| PA-7 | 14,6 | AC-2, AC-3, SC-3 |

O Azure RBAC (controle de acesso baseado em função) do Azure permite que você gerencie o acesso a recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, a entidades de serviço de grupo e a identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como CLI do Azure, Azure PowerShell e o portal do Azure. Os privilégios que você atribui aos recursos por meio do RBAC do Azure devem ser sempre limitados ao que é exigido pelas funções. Os privilégios limitados complementam a abordagem JIT (just in time) do Azure AD Privileged Identity Management (PIM), e esses privilégios devem ser revisados periodicamente.
Use funções internas para alocar a permissão e somente crie uma função personalizada quando necessário. 

- [O que é o controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md)

- [Como configurar o RBAC do Azure](../../role-based-access-control/role-assignments-portal.md)

- [Como usar as revisões de identidade e acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Gerenciamento de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: escolha o processo de aprovação para o suporte da Microsoft 

| ID do Azure | Controles do CIS v 7.1 ID (s) | ID (s) do NIST SP 800-53 R4 |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

Em cenários de suporte em que a Microsoft precisa acessar dados do cliente, Sistema de Proteção de Dados do Cliente fornece um recurso para que você reveja explicitamente e aprove ou rejeite cada solicitação de acesso a dados do cliente.

- [Entender Sistema de Proteção de Dados do Cliente](../fundamentals/customer-lockbox-overview.md)

**Responsabilidade**: Cliente

**Participantes da segurança do cliente** ([saiba mais](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Segurança de aplicativo e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gerenciamento de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Gerenciamento de identidades e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)
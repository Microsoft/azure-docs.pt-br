---
title: Controle de segurança do Azure – identidade e controle de acesso
description: Controle de acesso e identidade do controle de segurança do Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 08ba75b5160acb20cb5e2d172203ae48b546f17d
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202225"
---
# <a name="security-control-identity-and-access-control"></a>Controle de segurança: identidade e controle de acesso

As recomendações de gerenciamento de identidade e acesso se concentram em resolver problemas relacionados ao controle de acesso baseado em identidade, bloqueando o acesso administrativo, alertando sobre eventos relacionados à identidade, comportamento anormal da conta e controle de acesso baseado em função.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3.1 | 4.1 | Cliente |

O Azure AD tem funções internas que devem ser explicitamente atribuídas e podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3.2 | 4.2 | Cliente |

O Azure AD não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3.3 | 4.3 | Cliente |

Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Você também pode habilitar um acesso just-in-time/apenas o suficiente usando Azure AD Privileged Identity Management funções privilegiadas para serviços da Microsoft e Azure Resource Manager. 

- [Saiba mais sobre o Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3.4 | 4.4 | Cliente |

Sempre que possível, use Azure Active Directory SSO em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Entender o SSO com o Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3,5 | 4,5, 11,5, 12,11, 16,3 | Cliente |

Habilite o Azure AD MFA e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como habilitar a MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../../security-center/security-center-identity-access.md)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3,6 | 4,6, 11,6, 12,12 | Cliente |

Use PAWs (estações de trabalho com acesso privilegiado) com a MFA configurada para fazer logon e configurar os recursos do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3.7 | 4,8, 4,9 | Cliente |

Use Azure Active Directory relatórios de segurança para a geração de logs e alertas quando atividades suspeitas ou inseguras ocorrerem no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../../security-center/security-center-identity-access.md)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3.8 | 11,7 | Cliente |

Use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas no Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

## <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Cliente |

Use Azure Active Directory como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como criar e configurar uma instância do Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3.10 | 16,9, 16,10 | Cliente |

O Azure AD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo. 

- [Entender os relatórios do Azure AD](../../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../../active-directory/governance/access-reviews-overview.md)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3,11 | 16,12 | Cliente |

Você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração com qualquer ferramenta de monitoramento/SIEM.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure Active Directory e enviando os logs de auditoria e os logs de entrada para um workspace do Log Analytics. Você pode configurar os alertas desejados no workspace do Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3,12 | 16,13 | Cliente |

Use os recursos de proteção de risco e identidade do Azure AD para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure Active Directory](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 3,13 | 16 | Cliente |

Em cenários de suporte em que a Microsoft precisa acessar dados do cliente, Sistema de Proteção de Dados do Cliente fornece uma interface para você examinar e aprovar ou rejeitar solicitações de acesso a dados do cliente.

- [Entender Sistema de Proteção de Dados do Cliente](../fundamentals/customer-lockbox-overview.md)


## <a name="next-steps"></a>Próximas etapas

- Consulte o próximo controle de segurança: [proteção de dados](security-control-data-protection.md)
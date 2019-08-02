---
title: Conteúdo da função de administrador para serviços de Microsoft 365 – Azure AD | Microsoft Docs
description: Encontre referências de conteúdo e API para funções de administrador para serviços de Microsoft 365 no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12ffa1d4f3e802b3419b31844ddd8133ceab5086
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304387"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Funções de administrador para serviços Microsoft 365s

Todos os produtos do Microsoft 365 podem ser gerenciados com funções administrativas no Azure AD. Alguns produtos também fornecem funções adicionais que são específicas a esse produto. Para obter informações sobre as funções compatíveis com cada produto, veja a tabela a seguir. Discussões gerais sobre problemas de delegação podem ser encontradas no [Planejamento de delegação de função no Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Onde encontrar conteúdo

Serviço de Microsoft 365 | Conteúdo de função | Conteúdo de API
---------------------- | ------------------ | -----------------
Funções de administrador em planos de negócios do Office 365 e do Microsoft 365 | [Funções de administrador do Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Não disponível
Azure AD (Azure Active Directory) e Azure AD Identity Protection| [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [API do Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Controle de acesso baseado em função do Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell para Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Buscar atribuições de função](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Funções de administrador do Azure AD](directory-assign-admin-roles.md)<br>Também [Sobre a função de administrador do SharePoint no Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [API do Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Equipes/Skype for Business | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [API do Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
O Centro de Conformidade e Segurança (Proteção Avançada contra Ameaças do Office 365, Proteção do Exchange Online, Proteção de Informações) | [Funções de administrador do Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Buscar atribuições de função](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Pontuação segura | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [API do Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Gerenciador de conformidade | [Funções do gerenciador de conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Não disponível
Proteção de Informações do Azure | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [API do Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Controle de acesso baseado em função](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Referência de API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Proteção Avançada contra Ameaças do Azure | [Grupos de funções do ATP do Azure](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Não disponível
Proteção Avançada contra Ameaças do Windows Defender | [Controle de acesso baseado em função do Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Não disponível
Privileged Identity Management | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [API do Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Controle de acesso baseado em função do Intune](https://docs.microsoft.com/intune/role-based-access-control) | [API do Graph](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Área de trabalho gerenciada | [Funções de administrador do Azure AD](directory-assign-admin-roles.md) | [API do Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Buscar atribuições de função](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Próximas etapas

* [Como atribuir ou remover funções de administrador do Azure AD](directory-manage-roles-portal.md)
* [Referência das funções de administrador do Azure AD](directory-assign-admin-roles.md)

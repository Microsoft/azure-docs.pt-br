---
title: Exemplo do PowerShell – atribuir usuário a um aplicativo do Proxy de Aplicativo
description: Exemplo do PowerShell que atribui um usuário a um aplicativo do Proxy de Aplicativo do Azure AD (Azure Active Directory).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 8405afef07f5d3f52becc1acf184e9a36db8971c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548522"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Atribuir um usuário a um aplicativo específico do Proxy de Aplicativo do Azure AD

Este exemplo de script do PowerShell permite atribuir um usuário a um aplicativo específico do Proxy de Aplicativo do Azure AD.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este exemplo requer o [módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou a [versão prévia do módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Explicação sobre o script

| Comando | Observações |
|---|---|
| [New-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/new-azureaduserapproleassignment) | Atribui um usuário a uma função de aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](/powershell/azure/active-directory/overview).

Para obter outros exemplos do PowerShell para o Proxy de Aplicativo, confira [Exemplos do PowerShell do Azure AD para o Proxy de Aplicativo do Azure AD](../application-proxy-powershell-samples.md).

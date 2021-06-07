---
title: Exemplo do PowerShell – listar informações estendidas para aplicativos do Proxy de Aplicativo
description: Exemplo do PowerShell que lista todos os aplicativos do Proxy de Aplicativo do Azure AD (Azure Active Directory) junto com a ID do aplicativo (AppId), o nome (DisplayName), a URL externa (ExternalUrl), a URL interna (InternalUrl) e o tipo de autenticação (ExternalAuthenticationType).
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 1b3234a13a0e3fac760a899ce66e7faa7e7b642c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377320"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Obter todos os aplicativos do Proxy de Aplicativo e listar informações estendidas

Este exemplo de script do PowerShell lista informações sobre todos os aplicativos do Proxy de Aplicativo do Azure AD (Azure Active Directory), incluindo a ID do aplicativo (AppId), o nome (DisplayName), a URL externa (ExternalUrl), a URL interna (InternalUrl), o tipo de autenticação (ExternalAuthenticationType), o modo de SSO e outras configurações.

A alteração do valor da variável $ssoMode habilita uma saída filtrada pelo modo SSO. Os detalhes estão documentados no script.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este exemplo requer o [módulo do PowerShell do AzureAD V2 para o Graph (AzureAD)](/powershell/azure/active-directory/install-adv2).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Explicação sobre o script

| Comando | Observações |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Obtém uma entidade de serviço. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Obtém um aplicativo do Azure AD. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Recupera um aplicativo configurado para o Proxy de Aplicativo no Azure AD. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](/powershell/azure/active-directory/overview).

Para obter outros exemplos do PowerShell para o Proxy de Aplicativo, confira [Exemplos do PowerShell do Azure AD para o Proxy de Aplicativo do Azure AD](../application-proxy-powershell-samples.md).

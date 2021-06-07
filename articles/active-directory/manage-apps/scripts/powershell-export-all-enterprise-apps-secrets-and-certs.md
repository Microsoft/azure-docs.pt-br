---
title: Exemplo do PowerShell – exportar segredos e certificados para aplicativos empresariais no locatário do Azure Active Directory.
description: Exemplo do PowerShell que exporta todos os segredos e certificados dos aplicativos empresariais especificados em seu locatário do Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 536197ebc5df94447f3937773e0447e47961bd92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378594"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>Exportar segredos e certificados para aplicativos empresariais
Este exemplo de script do PowerShell exporta todos os segredos, certificados e proprietários para os aplicativos empresariais especificados do seu diretório para um arquivo CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Este exemplo requer o [módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou a [versão prévia do módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>Explicação sobre o script

O comando "Add-Member" é responsável por criar as colunas no arquivo CSV.
Você pode modificar a variável "$Path" diretamente no PowerShell, com um caminho de arquivo CSV, caso prefira que a exportação seja não interativa.

| Comando | Observações |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | Recupera um aplicativo empresarial do seu diretório. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | Recupera os proprietários de um aplicativo empresarial do seu diretório. |


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](/powershell/azure/active-directory/overview).

Confira outros exemplos do PowerShell para o Gerenciamento de Aplicativo em [Exemplos do PowerShell do Azure AD para o Gerenciamento de Aplicativo](../app-management-powershell-samples.md).

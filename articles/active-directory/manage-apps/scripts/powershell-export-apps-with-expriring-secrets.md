---
title: Exemplo do PowerShell – Exportar aplicativos com segredos e certificados com data de expiração no locatário do Azure Active Directory.
description: Exemplo do PowerShell que exporta todos os aplicativos com segredos e certificados com data de expiração para os aplicativos especificados em seu locatário do Azure Active Directory.
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
ms.openlocfilehash: 7f129e06904497b43eff8a3f0221fb57565ac112
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375399"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>Exportar aplicativos com segredos e certificados com data de expiração

Este exemplo de script do PowerShell exporta todos os Registros de aplicativo com segredos e certificados com data de expiração, bem como seus proprietários, para os aplicativos especificados de seu diretório em um arquivo CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Este exemplo requer o [módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou a [versão prévia do módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>Explicação sobre o script

O script pode ser usado diretamente sem modificações. O administrador será perguntado sobre a data de validade e se deseja ver ou não os segredos ou certificados já expirados.

O comando "Add-Member" é responsável por criar as colunas no arquivo CSV.
O comando "New-Object" cria um objeto a ser usado para as colunas na exportação do arquivo CSV.
Você pode modificar a variável "$Path" diretamente no PowerShell, com um caminho de arquivo CSV, caso prefira que a exportação seja não interativa.

| Comando | Observações |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Recupera um aplicativo do diretório. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Recupera os proprietários de um aplicativo do diretório. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](/powershell/azure/active-directory/overview).

Confira outros exemplos do PowerShell para o Gerenciamento de Aplicativo em [Exemplos do PowerShell do Azure AD para o Gerenciamento de Aplicativo](../app-management-powershell-samples.md).

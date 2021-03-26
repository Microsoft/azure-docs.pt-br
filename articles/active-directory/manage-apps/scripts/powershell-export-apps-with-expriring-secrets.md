---
title: Exemplo do PowerShell – Exportar aplicativos com segredos e certificados com data de expiração no locatário do Azure Active Directory.
description: Exemplo do PowerShell que exporta todos os aplicativos com segredos e certificados com data de expiração para os aplicativos especificados em seu locatário do Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: def9b55a1d873cccda5d1c48921e3f098beeced1
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149691"
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
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | Recupera um aplicativo de seu diretório. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | Recupera os proprietários de um aplicativo de seu diretório. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](/powershell/azure/active-directory/overview).

Confira outros exemplos do PowerShell para o Gerenciamento de Aplicativos em [Exemplos do PowerShell do Azure AD para o Gerenciamento de Aplicativos](../app-management-powershell-samples.md).

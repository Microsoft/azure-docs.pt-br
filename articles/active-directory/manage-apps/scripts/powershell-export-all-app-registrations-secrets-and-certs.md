---
title: Exemplo do PowerShell – exportar segredos e certificados para registros de aplicativo no locatário do Azure Active Directory.
description: Exemplo do PowerShell que exporta todos os segredos e certificados dos registros de aplicativo especificados em seu locatário do Azure Active Directory.
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
ms.openlocfilehash: d0de96d0d8a5edc6fbacc25dcbcb868073e57183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556546"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>Exportar segredos e certificados para registros de aplicativo

Este exemplo de script do PowerShell exporta todos os segredos e certificados dos registros de aplicativo especificados em seu diretório para um arquivo CSV.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

Este exemplo requer o [módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou a [versão prévia do módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>Explicação sobre o script

O comando "Add-Member" é responsável por criar as colunas no arquivo CSV.
Você pode modificar a variável "$Path" diretamente no PowerShell, com um caminho de arquivo CSV, caso prefira que a exportação seja não interativa.

| Comando | Observações |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Recupera um aplicativo do diretório. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | Recupera os proprietários de um aplicativo do diretório. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](/powershell/azure/active-directory/overview).

Confira outros exemplos do PowerShell para o Gerenciamento de Aplicativo em [Exemplos do PowerShell do Azure AD para o Gerenciamento de Aplicativo](../app-management-powershell-samples.md).

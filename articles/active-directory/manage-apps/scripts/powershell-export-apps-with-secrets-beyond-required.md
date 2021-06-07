---
title: Exemplo do PowerShell – Exportar aplicativos com segredos e certificados com data de expiração além da data necessária no locatário do Azure Active Directory.
description: Exemplo do PowerShell que exporta todos os aplicativos com segredos e certificados com data de expiração além da data necessária para os aplicativos especificados em seu locatário do Azure Active Directory.
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
ms.openlocfilehash: 692ab2cfd480fd15760c13c63922fe76d23058ea
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375382"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Exportar aplicativos com segredos e certificados com data de expiração além da data necessária

Este exemplo de script do PowerShell exporta todos os segredos e certificados de Registros de aplicativo com data de expiração além de um período necessário para os aplicativos especificados de seu diretório em um arquivo CSV de maneira não interativa.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script está funcionando de maneira não interativa. O administrador que o utilizar precisará alterar os valores na seção "#PARAMETERS TO CHANGE" e usar sua ID de Aplicativo, Segredo do Aplicativo, Nome do Locatário, o período da expiração das credenciais dos aplicativos e o caminho em que o CSV será exportado.
Esse script usa o [Fluxo de OAuth Client_Credential](../../develop/v2-oauth2-client-creds-grant-flow.md); a função "RefreshToken" compilará o token de acesso com base nos valores dos parâmetros modificados pelo administrador.

O comando "Add-Member" é responsável por criar as colunas no arquivo CSV.

| Comando | Observações |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Envia solicitações HTTP e HTTPS a uma página da Web ou serviço Web. Ele analisa a resposta e retorna conjuntos de links, imagens e outros elementos HTML significativos. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](/powershell/azure/active-directory/overview).

Confira outros exemplos do PowerShell para o Gerenciamento de Aplicativo em [Exemplos do PowerShell do Azure AD para o Gerenciamento de Aplicativo](../app-management-powershell-samples.md).

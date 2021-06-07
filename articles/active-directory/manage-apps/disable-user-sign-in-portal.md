---
title: Desabilitar as entradas de usuário para um aplicativo empresarial no Azure AD
description: Como desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele no Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 783de636d4520f48f624f3380f811f1f18366330
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259229"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Desabilitar entradas de usuário em um aplicativo empresarial no Azure Active Directory

É fácil desabilitar um aplicativo empresarial para que nenhum usuário possa entrar nele no Azure Active Directory (AD do Azure). Você precisa das permissões apropriadas para gerenciar o aplicativo empresarial. E você deve ser administrador global para o diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como desabilitar entradas de usuário?

1. Entre no [Portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
1. Selecione **todos os serviços**, insira **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.
1. No painel **Azure Active Directory**  -   **_DirectoryName_*_ (ou seja, o painel do Azure ad para o diretório que você está gerenciando), selecione _* aplicativos empresariais**.
1. No painel **aplicativos empresariais – todos os aplicativos** , você vê uma lista dos aplicativos que você pode gerenciar. Selecione um aplicativo.
1. No painel ***AppName** _ (ou seja, o painel com o nome do aplicativo selecionado no título), selecione _ * Propriedades * *.
1. No painel ***AppName** _-_ *Properties**, selecione **não** para **habilitado para os usuários entrarem?**.
1. Escolha o comando **Salvar** .

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Usar o PowerShell do Azure AD para desabilitar um aplicativo não listado

Se você souber a AppId de um aplicativo que não aparece na lista de aplicativos empresariais (por exemplo, porque você excluiu o aplicativo ou a entidade de serviço ainda não foi criada porque o aplicativo está autorizado previamente pela Microsoft), você poderá criar manualmente a entidade de serviço para o aplicativo e, em seguida, desabilitá-lo usando o [cmdlet do PowerShell do AzureAD](/powershell/module/azuread/New-AzureADServicePrincipal).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Próximas etapas

* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](./assign-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](./add-application-portal-configure.md)

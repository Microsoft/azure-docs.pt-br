---
title: Ocultar um aplicativo empresarial da experiência do usuário no Azure AD
description: Como ocultar um aplicativo empresarial da experiência do usuário em painéis de acesso Azure Active Directory ou em iniciadores de Microsoft 365.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8469b48b92f3f9a645a0c05441e6c1943b02e16f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258873"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Ocultar aplicativos empresariais de usuários finais no Azure Active Directory

Instruções sobre como ocultar aplicativos do painel myapps ou Microsoft 365 iniciador dos usuários finais. Quando um aplicativo estiver oculto, os usuários ainda terão permissões para o aplicativo. 

## <a name="prerequisites"></a>Pré-requisitos

Os privilégios de administrador do aplicativo são necessários para ocultar um aplicativo do painel myapps e Microsoft 365 Launcher.

São necessários privilégios de administrador global para ocultar todos os Microsoft 365 aplicativos.


## <a name="hide-an-application-from-the-end-user"></a>Ocultar um aplicativo do usuário final
Use as etapas a seguir para ocultar um aplicativo do painel myapps e Microsoft 365 o iniciador de aplicativo.

1.  Entre no [portal do Azure](https://portal.azure.com) como o administrador global do diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **Aplicativos empresariais**. A folha **Aplicativos empresariais - Todos os aplicativos** abre.
4.  Em **Tipo de Aplicativo**, selecione **Aplicativos Empresariais**, se já não estiver selecionado.
5.  Procure o aplicativo que você quer ocultar e clique no aplicativo.  A visão geral do aplicativo é aberta.
6.  Clique em **Propriedades**. 
7.  Para a pergunta **Visível para os usuários?**, clique em **Não**.
8.  Clique em **Save** (Salvar).

> [!NOTE]
> Essas instruções se aplicam somente a aplicativos empresariais.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Usar o PowerShell do Azure AD para ocultar um aplicativo

Para ocultar um aplicativo do painel myapps, você pode adicionar manualmente a marca HideApp à entidade de serviço para o aplicativo. Execute os comandos do [PowerShell do AzureAD](/powershell/module/azuread/#service_principals) a seguir para definir a propriedade visível do aplicativo **para usuários?** como **não**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>Ocultar Microsoft 365 aplicativos do painel myapps

Use as etapas a seguir para ocultar todos os aplicativos Microsoft 365 do painel myapps. Os aplicativos ainda estão visíveis no portal do Office 365.

1.  Entre no [Portal do Azure](https://portal.azure.com) como um administrador global do diretório.
2.  Selecione **Azure Active Directory**.
3.  Selecione **Usuários**.
4.  Selecione **configurações do usuário**.
5.  Em **Aplicativos empresariais**, clique em **Gerenciar como os usuários finais inicializam e exibem os aplicativos.**
6.  Para **Usuários só podem ver aplicativos do Office 365 no portal do Office 365**, clique em **Sim**.
7.  Clique em **Save** (Salvar).

## <a name="next-steps"></a>Próximas etapas
* [Ver todos os meus grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](assign-user-or-group-access-portal.md)
* [Remover uma atribuição de usuário ou de grupo de um aplicativo empresarial](./assign-user-or-group-access-portal.md)
* [Alterar o nome ou logotipo de um aplicativo empresarial](./add-application-portal-configure.md)

---
title: Criar funções personalizadas no controle de acesso baseado em função do Azure AD | Microsoft Docs
description: Criar e atribuir funções do Azure AD personalizadas com escopo de recurso em recursos do Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0e1543f18c18c7fdf97c39f35ba38ded658392
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007809"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Criar e atribuir uma função personalizada no Azure Active Directory

Este artigo descreve como criar funções personalizadas no Azure AD (Azure Active Directory). Para ver as noções básicas de funções personalizadas, confira a [visão geral de funções personalizadas](custom-overview.md). A função pode ser atribuída apenas no escopo no nível do diretório ou em um escopo do recurso de registro do aplicativo.

As funções personalizadas podem ser criadas na guia [Funções e administradores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) na página de visão geral do Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Criar uma função no portal do Azure

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Criar uma função personalizada para permitir acesso para gerenciar registros de aplicativo

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de administrador de função privilegiada ou de administrador global na organização do Azure AD.
1. Selecione **Azure Active Directory** > **Funções e administradores** > **Nova função personalizada**.

   ![Criar ou editar funções na página Funções e administradores](./media/custom-create/new-custom-role.png)

1. Na guia **Noções básicas**, forneça um nome e uma descrição para a função e clique em **Próximo**.

   ![forneça um nome e uma descrição para uma função personalizada na guia Noções básicas](./media/custom-create/basics-tab.png)

1. Na guia **Permissões**, selecione as permissões necessárias para gerenciar propriedades básicas e propriedades de credenciais de registros de aplicativo. Para obter uma descrição detalhada de cada permissão, confira [Subtipos e permissões de registro de aplicativo no Azure Active Directory](custom-available-permissions.md).
   1. Primeiro, insira "credenciais" na barra de pesquisa e selecione a permissão `microsoft.directory/applications/credentials/update`.

      ![Selecione as permissões para uma função personalizada na guia Permissões](./media/custom-create/permissions-tab.png)

   1. Em seguida, insira "básico" na barra de pesquisa, selecione a permissão `microsoft.directory/applications/basic/update` e, em seguida, clique **Próximo**.
1. Na guia **Examinar + criar**, examine as permissões e selecione **Criar**.

Sua função personalizada aparecerá na lista de funções disponíveis a serem atribuídas.

## <a name="create-a-role-using-powershell"></a>Criar uma função usando o PowerShell

### <a name="prepare-powershell"></a>Preparar o PowerShell

Primeiramente, você deve [baixar o módulo de versão prévia do PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureADPreview).

Para instalar o módulo PowerShell do Azure AD, use os seguintes comandos:

``` PowerShell
Install-Module -Name AzureADPreview 
Import-Module -Name AzureADPreview 
```

Para verificar se o módulo está pronto para ser usado, use o seguinte comando:

``` PowerShell
Get-Module -Name AzureADPreview 

  ModuleType Version      Name                         ExportedCommands 
  ---------- ---------    ----                         ---------------- 
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...} 
```

### <a name="connect-to-azure"></a>Conectar-se ao Azure

Para se conectar ao Azure Active Directory, use o seguinte comando:

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>Criar a função personalizada

Crie uma função usando o seguinte script do PowerShell:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Atribuir a função personalizada usando o PowerShell do Azure AD

Atribua a função usando o script do PowerShell abaixo:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>Criar uma função com a API do Graph

1. Crie a definição da função.

    Solicitação HTTP para criar uma definição de função personalizada.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Corpo

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > O `"templateId": "GUID"` é um parâmetro opcional que é enviado no corpo, dependendo do requisito. Se você tiver um requisito para criar várias funções personalizadas diferentes com parâmetros comuns, é melhor criar um modelo e definir um `templateId` valor. Você pode gerar um `templateId` valor com antecedência usando o cmdlet do PowerShell `(New-Guid).Guid` . 

1. Crie a atribuição de função.

    Solicitação HTTP para criar uma definição de função personalizada.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Corpo

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Atribuir uma função personalizada com escopo para um recurso

Como funções internas, as funções personalizadas são atribuídas por padrão no escopo de toda a organização padrão para conceder permissões de acesso em todos os registros de aplicativo em sua organização. Contudo, diferentemente das funções internas, as funções personalizadas também podem ser atribuídas no escopo de um único recurso do Azure AD. Com isso, você pode oferecer ao usuário a permissão de atualizar credenciais e propriedades básicas de um único aplicativo sem precisar criar uma segunda função personalizada.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de desenvolvedor de aplicativo na organização do Azure AD.
1. Selecione **Registros do Aplicativo**.
1. Selecione o registro do aplicativo ao qual você está concedendo acesso para gerenciar. Talvez seja necessário selecionar **Todos os aplicativos** para ver a lista completa de registros de aplicativo em sua organização do Azure AD.

    ![Selecionar o registro do aplicativo como um escopo de recurso para uma atribuição de função](./media/custom-create/appreg-all-apps.png)

1. No registro do aplicativo, selecione **Funções e administradores**. Se você ainda não criou uma, as instruções estão no [procedimento anterior](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Selecione a função para abrir a página **Atribuições**.
1. Selecione **Adicionar atribuição** para adicionar um usuário. O usuário receberá as permissões apenas pelo registro do aplicativo selecionado.

## <a name="next-steps"></a>Próximas etapas

- Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](permissions-reference.md).
- Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context).
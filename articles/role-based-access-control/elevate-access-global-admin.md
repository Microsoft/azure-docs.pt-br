---
title: Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure
description: Descreve como elevar o acesso para um Administrador Global gerenciar todas as assinaturas e grupos de gerenciamento no Azure Active Directory usando a API REST ou o portal do Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: 850d50bc9e427ff559782d587d74b33089332a8d
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091639"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure

Como um Administrador Global no Azure AD (Azure Active Directory), talvez você não tenha acesso a todas as assinaturas e grupos de gerenciamento em seu diretório. Este artigo descreve maneiras de elevar o acesso para todas as assinaturas e grupos de gerenciamento.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Por que você precisa elevar o acesso?

Se você for um administrador global, pode haver ocasiões em que você deseja executar as seguintes ações:

- Recuperar o acesso a um grupo de gerenciamento ou assinatura do Azure quando um usuário tiver perdido o acesso
- Conceder a outro usuário ou a você mesmo acesso a uma assinatura ou grupo de gerenciamento do Azure
- Ver todas as assinaturas ou grupos de gerenciamento do Azure em uma organização
- Permitir o acesso de um aplicativo de automação (como um aplicativo de faturamento ou de auditoria) a todas as assinaturas do Azure ou grupos de gerenciamento

## <a name="how-does-elevated-access-work"></a>Como funciona o acesso elevado?

Os recursos do Azure AD e do Azure são protegidos independentemente um do outro. Ou seja, as atribuições de função do Azure AD não concedem acesso aos recursos do Azure, e as atribuições de função do Azure não concedem acesso ao Azure AD. No entanto, se você for um [administrador global](../active-directory/roles/permissions-reference.md#global-administrator-permissions) no Azure AD, você poderá atribuir a si mesmo acesso a todas as assinaturas e grupos de gerenciamento do Azure em seu diretório. Use esse recurso se você não tiver acesso aos recursos do Azuresubscription, como máquinas virtuais ou contas de armazenamento, e quiser usar o privilégio de administrador global para obter acesso a esses recursos.

Quando você elevar seu acesso, você receberá a função [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) no Azure no escopo da raiz (`/`).Isso permite que você visualize todos os recursos e atribua acesso a qualquer assinatura ou grupo de gerenciamento no diretório. As atribuições de função de administrador de acesso do usuário podem ser removidas usando Azure PowerShell, CLI do Azure ou a API REST.

Você deve remover esse acesso elevado depois de fazer as alterações necessárias no escopo raiz.

![Elevar o acesso](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Portal do Azure

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso de um administrador global

Siga estas etapas para elevar o acesso de um administrador global usando o portal do Azure.

1. Faça login no [Portal do Azure](https://portal.azure.com) ou no [centro de administração do Active Directory do Azure](https://aad.portal.azure.com) como Administrador Global.

    Se você estiver usando Azure AD Privileged Identity Management, [Ative sua atribuição de função de administrador global](../active-directory/privileged-identity-management/pim-how-to-activate-role.md).

1. Abra **Azure Active Directory**.

1. Em **Gerenciar**, selecione **Propriedades**.

   ![Selecionar propriedades para propriedades de Azure Active Directory – captura de tela](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Em **Gerenciamento de acesso para recursos do Azure**, defina a alternância como **Sim**.

   ![Gerenciamento de acesso para recursos do Azure - captura de tela](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Quando você define a alternância para **Sim**, você recebe a função Administrador de acesso do usuário no RBAC do Azure no escopo raiz (/). Isso concede a você permissão para atribuir funções a todas as assinaturas e grupos de gerenciamento do Azure associados a esse diretório do AD do Azure. Essa alternância está disponível apenas para usuários com a função de administrador global no Azure AD.

   Quando você define a alternância como **Não**, a função Administrador de Acesso do Usuário no RBAC do Azure é removida da sua conta de usuário. Você não pode mais atribuir funções a todas as assinaturas e grupos de gerenciamento do Azure associados a esse diretório do AD do Azure. Você pode exibir e gerenciar somente as assinaturas do Azure e os grupos de gerenciamento aos quais você recebeu acesso.

    > [!NOTE]
    > Se você estiver usando [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), a desativação de sua atribuição de função não alterará o **Gerenciamento de acesso para recursos do Azure** alternar para **não**. Para manter o acesso com privilégios mínimos, recomendamos que você defina essa alternância como **não** antes de desativar sua atribuição de função.
    
1. Clique em **Salvar**, para salvar suas configurações.

   Essa configuração não é uma propriedade global, aplicando-se somente ao usuário conectado no momento. Você não pode elevar o acesso para todos os membros da função de Administrador Global.

1. Saia e entre novamente para atualizar o seu acesso.

    Agora você deve ter acesso a todas as assinaturas e grupos de gerenciamento em seu diretório. Ao exibir o painel de controle de acesso (IAM), você notará que recebeu a função Administrador de acesso do usuário no escopo raiz.

   ![Atribuições de função de assinatura com escopo de raiz – captura de tela](./media/elevate-access-global-admin/iam-root.png)

1. Faça as alterações que você precisa fazer em acesso elevado.

    Para obter informações sobre como atribuir funções, consulte [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](role-assignments-portal.md). Se você estiver usando Privileged Identity Management, consulte [descobrir recursos do Azure para gerenciar](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) ou [atribuir funções de recurso do Azure](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Execute as etapas na seção a seguir para remover o acesso elevado.

### <a name="remove-elevated-access"></a>Remover acesso elevado

Para remover a atribuição de função de administrador de acesso do usuário no escopo raiz ( `/` ), siga estas etapas.

1. Entre como o mesmo usuário que foi usado para elevar o acesso.

1. Na lista de navegação, clique em **Azure Active Directory**, depois clique em **Propriedades**.

1. Defina a alternância de **Gerenciamento de acesso para recursos do Azure** de volta para **não**. Como essa é uma configuração por usuário, você deve estar conectado como o mesmo usuário que foi usado para elevar o acesso.

    Se você tentar remover a atribuição de função de administrador de acesso do usuário no painel controle de acesso (IAM), verá a seguinte mensagem. Para remover a atribuição de função, você deve definir a alternância de volta para **não** ou usar Azure PowerShell, CLI do Azure ou a API REST.

    ![Remover atribuições de função com escopo raiz](./media/elevate-access-global-admin/iam-root-remove.png)

1. Saia como administrador global.

    Se você estiver usando Privileged Identity Management, desative sua atribuição de função de administrador global.

    > [!NOTE]
    > Se você estiver usando [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), a desativação de sua atribuição de função não alterará o **Gerenciamento de acesso para recursos do Azure** alternar para **não**. Para manter o acesso com privilégios mínimos, recomendamos que você defina essa alternância como **não** antes de desativar sua atribuição de função.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Listar atribuição de função no escopo raiz (/)

Para listar a atribuição de função de administrador de acesso do usuário para um usuário no escopo raiz ( `/` ), use o comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) .

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Remover acesso elevado

Para remover a atribuição de função de administrador de acesso do usuário para você mesmo ou outro usuário no escopo raiz ( `/` ), siga estas etapas.

1. Entre como um usuário que possa remover o acesso com privilégios elevados. Esse pode ser o mesmo usuário que foi usado para elevar o acesso ou outro administrador global com acesso elevado no escopo raiz.

1. Use o comando [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) para remover a atribuição de função de Administrador de Acesso do Usuário.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>CLI do Azure

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso de um administrador global

Use as etapas básicas a seguir para elevar o acesso para um administrador global usando o CLI do Azure.

1. Use o comando [AZ REST](/cli/azure/reference-index?view=azure-cli-latest#az-rest) para chamar o `elevateAccess` ponto de extremidade, que concede a você a função de administrador de acesso do usuário no escopo raiz ( `/` ).

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Faça as alterações que você precisa fazer em acesso elevado.

    Para obter informações sobre como atribuir funções, consulte [Adicionar ou remover atribuições de função do Azure usando o CLI do Azure](role-assignments-cli.md).

1. Execute as etapas em uma seção posterior para remover o acesso elevado.

### <a name="list-role-assignment-at-root-scope-"></a>Listar atribuição de função no escopo raiz (/)

Para listar a atribuição de função de administrador de acesso do usuário para um usuário no escopo raiz ( `/` ), use o comando [AZ role Assignment List](/cli/azure/role/assignment#az-role-assignment-list) .

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Remover acesso elevado

Para remover a atribuição de função de administrador de acesso do usuário para você mesmo ou outro usuário no escopo raiz ( `/` ), siga estas etapas.

1. Entre como um usuário que possa remover o acesso com privilégios elevados. Esse pode ser o mesmo usuário que foi usado para elevar o acesso ou outro administrador global com acesso elevado no escopo raiz.

1. Use o comando [AZ role Assignment Delete](/cli/azure/role/assignment#az-role-assignment-delete) para remover a atribuição de função de administrador de acesso do usuário.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>API REST

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso de um administrador global

Use as etapas básicas a seguir para elevar o acesso de um Administrador global usando a API REST.

1. Usando REST, chame `elevateAccess` , que concede a você a função de administrador de acesso do usuário no escopo raiz ( `/` ).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Faça as alterações que você precisa fazer em acesso elevado.

    Para obter informações sobre como atribuir funções, consulte [Adicionar ou remover atribuições de função do Azure usando a API REST](role-assignments-rest.md).

1. Execute as etapas em uma seção posterior para remover o acesso elevado.

### <a name="list-role-assignments-at-root-scope-"></a>Listar atribuições de função no escopo raiz (/)

Você pode listar todas as atribuições de função para um usuário no escopo raiz ( `/` ).

- Chame [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope), em que `{objectIdOfUser}` é a ID de objeto do usuário cujas atribuições de função que você deseja recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Listar atribuições de negação no escopo raiz (/)

Você pode listar todas as atribuições de negação para um usuário no escopo raiz ( `/` ).

- Chame GET denyAssignments, em que `{objectIdOfUser}` é a ID de objeto do usuário cujas atribuições de negação você deseja recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Remover acesso elevado

Quando você chama `elevateAccess` , você cria uma atribuição de função para si mesmo, para revogar esses privilégios, é necessário remover a atribuição de função de administrador de acesso do usuário para você mesmo no escopo raiz ( `/` ).

1. Chame [GET roleDefinitions](/rest/api/authorization/roledefinitions/get), em que `roleName` é igual a Administrador de Acesso do Usuário, para determinar a ID do nome da função de Administrador de Acesso do Usuário.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Salve a ID do parâmetro `name`, nesse caso: `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

1. Você também precisa listar a atribuição de função para o administrador de diretório no escopo do diretório. Listar todas as atribuições no escopo de diretório para o `principalId` do administrador de diretório que fez a chamada de acesso elevar. Isso listará todas as atribuições no diretório para o objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Um administrador de diretório não deve ter muitas atribuições, se a consulta anterior retornar muitas atribuições, você também pode consultar todas as atribuições apenas no nível do escopo do diretório e, em seguida, filtrar os resultados: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. As chamadas anteriores retornam uma lista de atribuições de função. Encontre a atribuição de função em que o escopo é `"/"` e o `roleDefinitionId` termina com o ID do nome da função que você encontrou na etapa 1 e `principalId` corresponde ao objectId do administrador de diretório. 
    
    Atribuição de função de amostra:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Novamente, salve a ID do `name` parâmetro, neste caso, 11111111-1111-1111-1111-111111111111.

1. Por fim, use a ID da atribuição de função para remover a atribuição adicionada por `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Próximas etapas

- [Entender as diferentes funções](rbac-and-directory-admin-roles.md)
- [Adicionar ou remover atribuições de função do Azure usando a API REST](role-assignments-rest.md)

---
title: Adicionar ou remover atribuições de função do Azure usando o CLI do Azure-RBAC do Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, entidades de serviço ou identidades gerenciadas usando o CLI do Azure e o Azure RBAC (controle de acesso baseado em função) do Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 10060e0dc6595a4d59f3968fa324ca40c91a7a39
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033792"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Adicionar ou remover atribuições de função do Azure usando a CLI do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Este artigo descreve como atribuir funções usando CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)
- [Bash em Azure cloud Shell](../cloud-shell/overview.md) ou [CLI do Azure](/cli/azure)

## <a name="steps-to-add-a-role-assignment"></a>Etapas para adicionar uma atribuição de função

Para conceder acesso no Azure RBAC, adicione uma atribuição de função. Uma atribuição de função consiste em três elementos: entidade de segurança, definição de função e escopo. Para adicionar uma atribuição de função, siga estas etapas.

### <a name="step-1-determine-who-needs-access"></a>Etapa 1: determinar quem precisa de acesso

Você pode atribuir uma função a um usuário, grupo, entidade de serviço ou identidade gerenciada. Para adicionar uma atribuição de função, talvez seja necessário especificar a ID exclusiva do objeto. A ID tem o formato: `11111111-1111-1111-1111-111111111111`. Você pode obter a ID usando o portal do Azure ou CLI do Azure.

**Usuário**

Para um usuário do Azure AD, obtenha o nome principal do usuário, como *patlong \@ contoso.com* ou a ID de objeto de usuário. Para obter a ID de objeto, você pode usar [AZ ad User show](/cli/azure/ad/user#az_ad_user_show).

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Grupo**

Para um grupo do Azure AD, você precisa da ID de objeto de grupo. Para obter a ID de objeto, você pode usar [AZ ad Group show](/cli/azure/ad/group#az_ad_group_show) ou [AZ ad Group List](/cli/azure/ad/group#az_ad_group_list).

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Entidade de serviço**

Para uma entidade de serviço do Azure AD (identidade usada por um aplicativo), você precisa da ID de objeto da entidade de serviço. Para obter a ID de objeto, você pode usar [AZ ad SP List](/cli/azure/ad/sp#az_ad_sp_list). Para uma entidade de serviço, use a ID de objeto, e **não** a ID do aplicativo.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Identidade gerenciada**

Para uma identidade gerenciada atribuída pelo sistema ou pelo usuário, você precisa da ID de objeto. Para obter a ID de objeto, você pode usar [AZ ad SP List](/cli/azure/ad/sp#az_ad_sp_list).

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Para apenas listar identidades gerenciadas atribuídas pelo usuário, você pode usar a [lista de identidade AZ](/cli/azure/identity#az_identity_list).

```azurecli
az identity list
```
    
### <a name="step-2-find-the-appropriate-role"></a>Etapa 2: localizar a função apropriada

As permissões são agrupadas em funções. Você pode selecionar em uma lista de várias [funções internas do Azure](built-in-roles.md) ou pode usar suas próprias funções personalizadas. É uma prática recomendada conceder acesso com o privilégio mínimo necessário, portanto, evite atribuir uma função mais ampla.

Para listar funções e obter a ID de função exclusiva, você pode usar a [lista de definição de função AZ](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

Veja como listar os detalhes de uma função específica.

```azurecli
az role definition list --name "{roleName}"
```

Para obter mais informações, consulte [listar definições de função do Azure](role-definitions-list.md#azure-cli).
 
### <a name="step-3-identify-the-needed-scope"></a>Etapa 3: identificar o escopo necessário

O Azure fornece quatro níveis de escopo: recurso, [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups), assinatura e grupo de [Gerenciamento](../governance/management-groups/overview.md). É uma prática recomendada conceder acesso com o privilégio mínimo necessário, portanto, evite atribuir uma função em um escopo mais amplo. Para obter mais informações sobre escopo, confira [Noções básicas de escopo](scope-overview.md).

**Escopo do recurso**

Para o escopo de recurso, você precisa da ID de recurso para o recurso. Você pode encontrar a ID do recurso examinando as propriedades do recurso no portal do Azure. Uma ID de recurso tem o formato a seguir.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Escopo do grupo de recursos**

Para o escopo do grupo de recursos, você precisa do nome do grupo de recursos. Você pode encontrar o nome na página **grupos de recursos** na portal do Azure ou pode usar a [lista de grupos AZ](/cli/azure/group#az_group_list).

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Escopo da assinatura** 

Para o escopo da assinatura, você precisa da ID da assinatura. Você pode encontrar a ID na página de **assinaturas** no portal do Azure ou pode usar a [lista de contas AZ](/cli/azure/account#az_account_list).

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Escopo do grupo de gerenciamento** 

Para o escopo do grupo de gerenciamento, você precisa do nome do grupo de gerenciamento. Você pode encontrar o nome na página **grupos de gerenciamento** no portal do Azure ou pode usar o [Gerenciamento de conta AZ-lista de grupos](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-add-role-assignment"></a>Etapa 4: Adicionar atribuição de função

Para adicionar uma atribuição de função, use o comando [AZ role Assignment Create](/cli/azure/role/assignment#az_role_assignment_create) . Dependendo do escopo, o comando normalmente tem um dos formatos a seguir.

**Escopo do recurso**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Escopo do grupo de recursos**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Escopo da assinatura** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Escopo do grupo de gerenciamento** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

Veja a seguir um exemplo de saída quando você atribui a função de [colaborador de máquina virtual](built-in-roles.md#virtual-machine-contributor) a um usuário em um escopo de grupo de recursos.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="add-role-assignment-examples"></a>Adicionar exemplos de atribuição de função

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Adicionar atribuição de função para todos os contêineres de BLOB em um escopo de recurso da conta de armazenamento

Atribui a função de [colaborador de dados de blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) a uma entidade de serviço com a ID de objeto *55555555-5555-5555-5555-555555555555* em um escopo de recurso para uma conta de armazenamento denominada *storage12345*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Adicionar atribuição de função para um escopo de recurso de contêiner de blob específico

Atribui a função de [colaborador de dados do blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) a uma entidade de serviço com a ID de objeto *55555555-5555-5555-5555-555555555555* em um escopo de recurso para um contêiner de blob chamado *blob-container-01*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Adicionar atribuição de função para um grupo em um escopo de recurso de rede virtual específico

Atribui a função [colaborador da máquina virtual](built-in-roles.md#virtual-machine-contributor) ao grupo de *equipe Ana Mack* com ID 22222222-2222-2222-2222-222222222222 em um escopo de recurso para uma rede virtual denominada *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="add-role-assignment-for-a-user-at-a-resource-group-scope"></a>Adicionar atribuição de função para um usuário em um escopo de grupo de recursos

Atribui a função [colaborador da máquina virtual](built-in-roles.md#virtual-machine-contributor) ao usuário *patlong \@ contoso.com* no escopo do grupo de recursos *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Adicionar atribuição de função para um usuário usando a ID de função exclusiva em um escopo de grupo de recursos

Há algumas ocasiões em que um nome de função pode ser alterado, por exemplo:

- Você está usando sua própria função personalizada e decide alterar o nome.
- Você está usando uma função de visualização que tem **(visualização)** no nome. Quando a função é liberada, a função é renomeada.

Mesmo que uma função seja renomeada, a ID da função não será alterada. Se você estiver usando scripts ou automação para criar atribuições de função, é uma prática recomendada usar a ID de função exclusiva em vez do nome da função. Portanto, se uma função for renomeada, os scripts provavelmente funcionarão.

O exemplo a seguir atribui a função [colaborador da máquina virtual](built-in-roles.md#virtual-machine-contributor) ao usuário *patlong \@ contoso.com* no escopo do grupo de recursos *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-resource-group-scope"></a>Adicionar atribuição de função para todos os contêineres de BLOB em um escopo de grupo de recursos

Atribui a função de [colaborador de dados de blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) a uma entidade de serviço com a ID de objeto *55555555-5555-5555-5555-555555555555* no escopo do grupo de recursos *Storage-RG de exemplo-armazenamento* .

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

Como alternativa, você pode especificar o grupo de recursos totalmente qualificado com o `--scope` parâmetro:

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Adicionar atribuição de função para um aplicativo em um escopo de grupo de recursos

Atribui a função [colaborador de máquina virtual](built-in-roles.md#virtual-machine-contributor) a um aplicativo com ID de objeto de entidade de serviço 44444444-4444-4444-4444-444444444444 no escopo do grupo de recursos *Pharma-Sales* .

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope"></a>Adicionar atribuição de função para uma nova entidade de serviço em um escopo de grupo de recursos

Se você criar uma entidade de serviço e tentar atribuir uma função imediatamente a essa entidade de serviço, essa atribuição de função poderá falhar em alguns casos. Por exemplo, se você usar um script para criar uma nova identidade gerenciada e, em seguida, tentar atribuir uma função a essa entidade de serviço, a atribuição de função poderá falhar. Provavelmente, o motivo dessa falha é um atraso de replicação. A entidade de serviço é criada em uma região; no entanto, a atribuição de função pode ocorrer em uma região diferente que ainda não tenha replicado a entidade de serviço. Para resolver esse cenário, você deve especificar o tipo de entidade de segurança ao criar a atribuição de função.

Para adicionar uma atribuição de função, use [AZ role Assignment Create](/cli/azure/role/assignment#az_role_assignment_create), especifique um valor para `--assignee-object-id` e, em seguida, defina `--assignee-principal-type` como `ServicePrincipal` .

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

O exemplo a seguir atribui a função [colaborador de máquina virtual](built-in-roles.md#virtual-machine-contributor) à identidade gerenciada de *teste de MSI* no escopo do grupo de recursos *Pharma-Sales* :

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Adicionar atribuição de função para um usuário em um escopo de assinatura

Atribui a função [leitor](built-in-roles.md#reader) ao usuário *annm \@ example.com* em um escopo de assinatura.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-group-at-a-subscription-scope"></a>Adicionar atribuição de função para um grupo em um escopo de assinatura

Atribui a função [leitor](built-in-roles.md#reader) ao grupo de *equipe Ana Mack* com ID 22222222-2222-2222-2222-222222222222 em um escopo de assinatura.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-subscription-scope"></a>Adicionar atribuição de função para todos os contêineres de BLOB em um escopo de assinatura

Atribui a função de [leitor de dados de blob de armazenamento](built-in-roles.md#storage-blob-data-reader) ao usuário *Alain \@ example.com* em um escopo de assinatura.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Adicionar atribuição de função para um usuário em um escopo de grupo de gerenciamento

Atribui a função de [leitor de cobrança](built-in-roles.md#billing-reader) ao usuário *Alain \@ example.com* em um escopo de grupo de gerenciamento.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

No RBAC do Azure, para remover o acesso, você remove uma atribuição de função usando [AZ role Assignment Delete](/cli/azure/role/assignment#az_role_assignment_delete).

O exemplo a seguir remove a atribuição de função de [colaborador de máquina virtual](built-in-roles.md#virtual-machine-contributor) do usuário *patlong \@ contoso.com* no grupo de recursos *Pharma-Sales* :

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Remove a função [leitor](built-in-roles.md#reader) do grupo de *equipe Ana Mack* com ID 22222222-2222-2222-2222-222222222222 em um escopo de assinatura.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Remove a função de [leitor de cobrança](built-in-roles.md#billing-reader) do usuário *Alain \@ example.com* no escopo do grupo de gerenciamento.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>Próximas etapas

- [Listar atribuições de função do Azure usando CLI do Azure](role-assignments-list-cli.md)
- [Use a CLI do Azure para gerenciar recursos e grupos de recursos do Azure](../azure-resource-manager/management/manage-resources-cli.md)

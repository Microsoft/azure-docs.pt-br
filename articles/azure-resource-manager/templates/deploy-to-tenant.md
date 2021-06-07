---
title: Implantar recursos no locatário
description: Descreve como implantar recursos no escopo do locatário em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: fd5a9ae60c578a3be7f70d82baae0a15e406b9db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491479"
---
# <a name="tenant-deployments-with-arm-templates"></a>Implantações de locatário com modelos ARM

À medida que sua organização amadureceu, talvez seja necessário definir e atribuir [políticas](../../governance/policy/overview.md) ou o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) em seu locatário do Azure AD. Com os modelos de nível de locatário, você pode aplicar políticas declarativamente e atribuir funções em um nível global.

## <a name="supported-resources"></a>Recursos compatíveis

Nem todos os tipos de recursos podem ser implantados no nível de locatário. Esta seção lista os tipos de recursos com suporte.

Para políticas do Azure, use:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Para o controle de acesso baseado em função do Azure (RBAC do Azure), use:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

Para modelos aninhados que são implantados em grupos de gerenciamento, assinaturas ou grupos de recursos, use:

* [implantações](/azure/templates/microsoft.resources/deployments)

Para criar grupos de gerenciamento, use:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Para criar assinaturas, use:

* [aliases](/azure/templates/microsoft.subscription/aliases)

Para gerenciar custos, use:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [sobre](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Para configurar o portal, use:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Esquema

O esquema que você pode usar para implantações de locatário são diferentes dos esquemas para implantações do grupo de recursos.

Para modelos, use:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

O esquema para um arquivo de parâmetro é o mesmo para todos os escopos de implantação. Para arquivos de parâmetros, use:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>Acesso necessário

A entidade de segurança que está implantando o modelo deve ter permissões para criar recursos no escopo do locatário. A entidade de segurança deve ter permissão para executar as ações de implantação (`Microsoft.Resources/deployments/*`) e para criar os recursos definidos no modelo. Por exemplo, para criar um grupo de gerenciamento, a entidade de segurança deve ter permissão de colaborador no escopo do locatário. Para criar atribuições de função, a entidade de segurança deve ter permissão de proprietário.

O administrador global do Microsoft Azure Active Directory não tem automaticamente permissão para atribuir funções. Para habilitar implantações de modelo no escopo do locatário, o administrador global deve executar as seguintes etapas:

1. Eleve o acesso à conta para que o administrador global possa atribuir funções. Para obter mais informações, confira [Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Atribua o proprietário ou colaborador à entidade de segurança que precisa implantar os modelos.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

O principal agora tem as permissões necessárias para implantar o modelo.

## <a name="deployment-commands"></a>Comandos de implantação

Os comandos para implantações de locatário são diferentes dos comandos para implantações do grupo de recursos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para CLI do Azure, use [az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para Azure PowerShell, use [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Para obter informações mais detalhadas sobre os comandos de implantação e opções para implantar modelos de ARM, consulte:

* [Implantar recursos com modelos ARM e portal do Azure](deploy-portal.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](deploy-cli.md)
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](deploy-powershell.md)
* [Implantar recursos com modelos ARM e Azure Resource Manager API REST](deploy-rest.md)
* [Usar um botão de implantação para implantar modelos do repositório GitHub](deploy-to-azure-button.md)
* [Implantar modelos de ARM de Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Nome e local da implantação

Para implantações no nível do locatário, você deve fornecer um local para a implantação. O local da implantação é separado do local dos recursos que você implanta. O local de implantação especifica onde armazenar os dados de implantação. As implantações de [assinatura](deploy-to-subscription.md) e [grupo de gerenciamento](deploy-to-management-group.md) também exigem um local. Para implantações de [grupo de recursos](deploy-to-resource-group.md) , o local do grupo de recursos é usado para armazenar os dados de implantação.

Você pode fornecer um nome da implantação ou usar o nome da implantação padrão. O nome padrão é o nome do arquivo de modelo. Por exemplo, implantar um modelo chamado _azuredeploy.json_ cria um nome de implantação padrão de **azuredeploy**.

O local não pode ser alterado para cada nome de implantação. Você não pode criar uma implantação em um local quando há uma implantação existente com o mesmo nome em um local diferente. Por exemplo, se você criar uma implantação de locatário com o nome **deployment1** em **centralus**, não será possível criar outra implantação mais tarde com o nome **deployment1** , mas um local de **westus**. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local que a implantação anterior para esse nome.

## <a name="deployment-scopes"></a>Escopos de implantação

Ao implantar em um locatário, você pode implantar recursos em:

* o locatário
* grupos de gerenciamento dentro do locatário
* subscriptions
* grupos de recursos

Um [recurso de extensão](scope-extension-resources.md) pode ser definido como escopo para um destino diferente do destino de implantação.

O usuário que está implantando o modelo deve ter acesso ao escopo especificado.

Esta seção mostra como especificar escopos diferentes. Você pode combinar esses escopos diferentes em um único modelo.

### <a name="scope-to-tenant"></a>Escopo para o locatário

Os recursos definidos na seção de recursos do modelo são aplicados ao locatário.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Escopo do grupo de gerenciamento

Para direcionar um grupo de gerenciamento dentro do locatário, adicione uma implantação aninhada e especifique a `scope` propriedade.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Escopo da assinatura

Você também pode direcionar as assinaturas dentro do locatário. O usuário que está implantando o modelo deve ter acesso ao escopo especificado.

Para direcionar uma assinatura no locatário, use uma implantação aninhada e a `subscriptionId` propriedade.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Escopo para o grupo de recursos

Você também pode direcionar grupos de recursos dentro do locatário. O usuário que está implantando o modelo deve ter acesso ao escopo especificado.

Para direcionar um grupo de recursos dentro do locatário, use uma implantação aninhada. Definir as propriedades `subscriptionId` e `resourceGroup`. Não defina um local para a implantação aninhada porque ela é implantada no local do grupo de recursos.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Criar grupo de gerenciamento

O modelo a seguir cria um grupo de recursos vazio.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Se sua conta não tiver permissão para implantar no locatário, você ainda poderá criar grupos de gerenciamento implantando em outro escopo. Para obter mais informações, consulte [grupo de gerenciamento](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Atribuir função

O modelo a seguir atribui uma função no escopo do locatário.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como atribuir funções, confira [Adicionar atribuições de função do Azure usando modelos de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Você também pode implantar modelos no [nível de assinatura](deploy-to-subscription.md) e [nível do grupo de gerenciamento](deploy-to-management-group.md).

---
title: Bloquear recursos do Azure para evitar alterações | Microsoft Docs
description: Impeça que os usuários atualizem ou excluam recursos críticos do Azure ao aplicar um bloqueio a todos os usuários e funções.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: 31d77b4ea6e7594cd3ed4dba264f9ea6db4ca290
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155208"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloquear recursos para evitar alterações inesperadas 

Como administrador, talvez você precise bloquear uma assinatura, um recurso ou grupo de recursos para impedir que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos. É possível definir o nível de bloqueio como **CanNotDelete** ou **ReadOnly**. No portal, os bloqueios são chamados **Excluir** e **Somente leitura** respectivamente.

* **CanNotDelete** significa que os usuários autorizados ainda poderão ler e modificar um recurso, mas não poderão excluir o recurso. 
* **ReadOnly** significa que os usuários autorizados poderão ler um recurso, mas não poderão excluir ou atualizar o recurso. Aplicar esse bloqueio é semelhante ao restringir todos os usuários autorizados para as permissões concedidas pela função **Leitor**. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Como os bloqueios são aplicados

Quando você aplica um bloqueio a um escopo pai, todos os recursos filho herdam o mesmo bloqueio. Até mesmo os recursos que você adiciona posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança terá precedência.

Ao contrário do controle de acesso baseado em função, é possível usar bloqueios de gerenciamento para aplicar uma restrição a todos os usuários e a todas as funções. Para saber mais sobre como configurar permissões para usuários e funções, veja [Controle de Acesso Baseado em Função do Azure](../role-based-access-control/role-assignments-portal.md).

Bloqueios do Resource Manager se aplicam apenas às operações que ocorrem no plano de gerenciamento, que consistem em operações enviadas para `https://management.azure.com`. Os bloqueios não restringem a maneira como os recursos executam suas próprias funções. Alterações de recursos são restritas, mas as operações de recursos não são restritas. Por exemplo, um bloqueio de ReadOnly em um banco de dados SQL impede que você de exclua ou modifique o banco de dados. Ele não impede você de criar, atualizar ou excluir dados no banco de dados. Transações de dados são permitidas porque essas operações não são enviadas para `https://management.azure.com`.

Aplicando **ReadOnly** pode levar a resultados inesperados porque algumas operações que parecem não modificar o recurso realmente exigem ações que são bloqueadas pelo bloqueio. O **ReadOnly** bloqueio pode ser aplicado ao recurso ou ao grupo de recursos que contém o recurso. Alguns exemplos comuns de operações que estão bloqueadas por um **ReadOnly** bloqueio são:

* Um **ReadOnly** bloqueio em uma conta de armazenamento impede que todos os usuários listem as chaves. A operação de lista de chaves é tratada por meio de uma solicitação POST, pois as chaves retornadas estão disponíveis para operações de gravação.

* A aplicação de um bloqueio **ReadOnly** em um recurso do Serviço de Aplicativo impedirá o Visual Studio Server Explorer de exibir os arquivos para o recurso, pois essa interação exige acesso de gravação.

* Um **ReadOnly** bloqueio em um grupo de recursos que contém uma máquina virtual impede que todos os usuários de iniciar ou reiniciar a máquina virtual. Essas operações exigem uma solicitação POST.

## <a name="who-can-create-or-delete-locks"></a>Quem pode criar ou excluir bloqueios
Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso às ações `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Das funções internas, somente **Proprietário** e **Administrador do Acesso de Usuário** recebem essas ações.

## <a name="managed-applications-and-locks"></a>Aplicativos e bloqueios

Alguns serviços do Azure, como o Azure Databricks, usam [aplicativos gerenciados](../managed-applications/overview.md) para implementar o serviço. Nesse caso, o serviço cria dois grupos de recursos. Um grupo de recursos contém uma visão geral do serviço e não está bloqueado. Grupo de recursos contém a infraestrutura para o serviço e está bloqueado.

Se você tentar excluir o grupo de recursos de infraestrutura, você obterá um erro informando que o grupo de recursos está bloqueado. Se você tentar excluir o bloqueio para o grupo de recursos de infraestrutura, você obterá um erro informando que o bloqueio não pode ser excluído porque ele pertence a um aplicativo de sistema.

Em vez disso, exclua o serviço, que também exclui o grupo de recursos de infraestrutura.

Para aplicativos gerenciados, selecione o serviço implantado.

![Selecione o serviço](./media/resource-group-lock-resources/select-service.png)

Observe que o serviço inclui um link para um **grupo de recursos gerenciado**. Esse grupo de recursos mantém a infraestrutura e está bloqueado. Ele não pode ser excluído diretamente.

![Mostrar grupo gerenciado](./media/resource-group-lock-resources/show-managed-group.png)

Para excluir tudo para o serviço, incluindo o grupo de recursos de infraestrutura bloqueada, selecione **excluir** para o serviço.

![Excluir serviço](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modelo

Ao usar um modelo do Resource Manager para implantar um bloqueio, você usa valores diferentes para o nome e tipo, dependendo do escopo do bloqueio.

Ao aplicar um bloqueio para um **recurso**, usar os seguintes formatos:

* name - `{resourceName}/Microsoft.Authorization/{lockName}`
* tipo- `{resourceProviderNamespace}/{resourceType}/providers/locks`

Ao aplicar um bloqueio para um **grupo de recursos** ou **assinatura**, usar os seguintes formatos:

* name - `{lockName}`
* tipo- `Microsoft.Authorization/locks`

O exemplo a seguir mostra um modelo que cria um plano de serviço de aplicativo, um site da Web e um bloqueio no site da Web. O tipo de recurso do bloqueio é o tipo de recurso do recurso a ser bloqueado e **/providers/bloqueios**. O nome do bloqueio é criado por meio da concatenação do nome do recurso com **/Microsoft.Authorization/** e o nome do bloqueio.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Para obter um exemplo de como definir um bloqueio em um grupo de recursos, consulte [criar um grupo de recursos e bloqueá-la](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Bloqueie recursos implantados com o Azure PowerShell usando o comando [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

Para bloquear um recurso, forneça o nome dele, seu tipo de recurso e o nome do grupo de recursos.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para bloquear um grupo de recursos, forneça o nome dele.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Para obter informações sobre um bloqueio, use [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Para obter todos os bloqueios em sua assinatura, use:

```azurepowershell-interactive
Get-AzResourceLock
```

Para obter todos os bloqueios de um recurso, use:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para obter todos os bloqueios de um grupo de recursos, use:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Para excluir um bloqueio, use:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>CLI do Azure

Bloqueie recursos implantados com a CLI do Azure usando o comando [az lock create](/cli/azure/lock#az-lock-create).

Para bloquear um recurso, forneça o nome dele, seu tipo de recurso e o nome do grupo de recursos.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Para bloquear um grupo de recursos, forneça o nome dele.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Para saber mais sobre um bloqueio, use [az lock list](/cli/azure/lock#az-lock-list). Para obter todos os bloqueios em sua assinatura, use:

```azurecli
az lock list
```

Para obter todos os bloqueios de um recurso, use:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Para obter todos os bloqueios de um grupo de recursos, use:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Para excluir um bloqueio, use:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>API REST
Você pode bloquear os recursos implantados com a [API REST para bloqueios de gerenciamento](https://docs.microsoft.com/rest/api/resources/managementlocks). A API REST permite que você crie e exclua bloqueios e recupere informações sobre bloqueios existentes.

Para criar um bloqueio, execute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

O escopo pode ser uma assinatura, grupo de recursos ou recurso. O nome do bloqueio é como você deseja chamar o bloqueio. Para a api-version, use **2016-09-01**.

Na solicitação, inclua um objeto JSON que especifica as propriedades do bloqueio.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a organização lógica de recursos, confira [Usando marcas para organizar os recursos](resource-group-using-tags.md)
* É possível aplicar restrições e convenções em sua assinatura com políticas personalizadas. Para saber mais, veja [O que é o Azure Policy?](../governance/policy/overview.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](/azure/architecture/cloud-adoption-guide/subscription-governance).


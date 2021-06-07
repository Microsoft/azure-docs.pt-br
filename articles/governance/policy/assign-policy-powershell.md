---
title: 'Guia de início rápido: nova atribuição de política usando o PowerShell'
description: Neste início rápido, use o Azure PowerShell para criar uma atribuição do Azure Policy para identificar recursos sem conformidade.
ms.date: 03/31/2021
ms.topic: quickstart
ms.openlocfilehash: 1043bd53fa2c494d34b0007a851b95afd937d991
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091405"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Guia de início rápido: criar uma atribuição de política para identificar recursos não compatíveis usando o Azure PowerShell

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. Neste guia de início rápido, você cria uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados. Ao concluir, você identificará máquinas virtuais que _não estão em conformidade_.

O módulo do Azure PowerShell é usado para gerenciar recursos do Azure da linha de comando do ou em scripts.
Este guia explica como usar o módulo Az para criar uma atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Antes de começar, verifique se a versão mais recente do Azure PowerShell está instalada. Confira [Instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps) para obter informações detalhadas.

- Registre o provedor de recursos Informações do Azure Policy usando o Azure PowerShell. O registro do provedor de recursos garante o funcionamento da assinatura com ele. Para registrar um provedor de recursos, você deve ter permissão para registrar a operação do provedor de recursos. Esta operação está incluída nas funções de Colaborador e de Proprietário. Execute o seguinte comando para registrar o provedor de recursos:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Para saber mais sobre como registrar e exibir provedores de recursos, consulte [Provedores de recursos e tipos](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste início rápido, crie uma atribuição de política para a definição _Auditar VMs sem discos gerenciados_. Esta definição de política identifica máquinas virtuais não usando discos gerenciados.

Execute o comando a seguir para criar uma nova atribuição de política:

```azurepowershell-interactive
# Get a reference to the resource group that is the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition to assign
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Os comandos anteriores usam as seguintes informações:

- **Nome** - O nome real da atribuição. Neste exemplo, usamos _audit-vm-manageddisks_.
- **DisplayName** - O nome de exibição da atribuição de política. Nesse caso, você está usando _Auditar VMs sem atribuição de discos gerenciados_.
- **Definição**: a definição da política, com base naquela que você está usando para criar a atribuição. Nesse caso, é a ID da definição de política _Auditar VMs que não usam discos gerenciados_.
- **Escopo**: um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos. Substitua o &lt;escopo&gt; pelo nome do seu grupo de recursos.

Agora você está pronto para identificar recursos fora de conformidade para entender o estado de conformidade do ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos fora de conformidade

Use as informações a seguir para identificar recursos que não são compatíveis com a atribuição de política que você criou. Execute os seguintes comandos:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Para obter mais informações sobre como obter o estado da política, confira [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Seus resultados devem se parecer com o exemplo a seguir:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

Os resultados correspondem ao que você vê na **Conformidade do recurso** de uma atribuição de política na exibição do portal do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Use o seguinte comando para remover a atribuição criada:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para identificar recursos sem conformidade em seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)
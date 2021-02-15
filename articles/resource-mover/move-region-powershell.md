---
title: Mover recursos entre regiões usando o PowerShell no Azure Resource mover
description: Saiba como mover recursos entre regiões usando o PowerShell no Azure Resource mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 0aca0e49d72025686cf44d434fa7a43ae0c86e0b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461103"
---
# <a name="move-resources-across-regions-in-powershell"></a>Mover recursos entre regiões no PowerShell

Saiba como mover os recursos do Azure para uma região diferente usando o PowerShell no Azure Resource mover. 

> [!NOTE]
> O Azure Resource Mover está em versão prévia no momento.



## <a name="before-you-start"></a>Antes de começar

- Sua assinatura do Azure deve ter acesso ao Resource mover e você deve ter permissões de [administrador de acesso de usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) ou [proprietário](../role-based-access-control/built-in-roles.md#owner) para a assinatura.
- O movimentador de recursos não controla alterações e atualizações, portanto, faça as alterações necessárias nos recursos, antes de começar a movê-las.
- Ao mover recursos com o PowerShell, não é possível editar atualmente as configurações de região de destino. Modifique essas configurações diretamente no Portal.
- Quando você adiciona recursos a uma coleção de movimentação, na preparação para movê-los para outra região, os metadados sobre a movimentação são armazenados em um grupo de recursos criado para fins de finalidade. Atualmente, esse grupo de recursos pode residir nas regiões leste dos EUA 2 ou Europa Setentrional. Os recursos do Azure podem ser movidos entre todas as regiões públicas usando metadados presentes em qualquer uma dessas regiões.

## <a name="sample-values"></a>Valores de exemplo

Estamos usando esses valores em nossos exemplos de script:

**Configuração** | **Valor** 
--- | ---
ID da assinatura | id da assinatura
Local do serviço de movimentação de recursos | Leste dos EUA 2
Grupo de recursos de metadados | RegionMoveRG-centralus-westcentralus
Local do grupo de recursos de metadados | Leste dos EUA 2
Mover nome da coleção | Movecollection-centralus-westcentralus
Região de origem |  centralus
Grupo de recursos de origem | PSDemoRM
Região de destino | westcentralus
Grupo de recursos de destino | PSDemoRMtgt
VM a ser movida | PSDemoVM

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na sua assinatura do Azure.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>Criar o grupo de recursos para metadados

Crie um grupo de recursos para manter os metadados da coleção de movimentação e as informações de configuração.

```azurepowershell-interactive
# Create the resource group for metadata
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Saída esperada**:

![Texto de saída após a criação do grupo de recursos](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>Registre o provedor de recursos

Registre o provedor de recursos Microsoft. Migration, para que o recurso Movercollection possa ser criado.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>Criar a coleção de movimentação

O objeto Movecollection armazena metadados e informações de configuração sobre os recursos que você deseja mover.

- Para que o objeto Movecollection acesse a assinatura na qual o serviço do Azure Resource mover está localizado, ele precisa de uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como identidade de serviço gerenciada (MSI)) que é confiável para a assinatura.
- A identidade é atribuída à função colaborador ou administrador de acesso do usuário para a assinatura de origem.
- Para atribuir uma função à identidade, você precisa de uma função na assinatura (como proprietário ou administrador de acesso do usuário), com estas permissões:
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft.Authorization/roleAssignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus" -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Saída esperada**:

![Texto de saída após criar mover coleção](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Atribuir uma identidade gerenciada 

Defina a ID da assinatura, recupere a entidade de identidade do objeto Movecollection e atribua as funções do Azure a ele.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -Name "MoveCollection-centralus-westcentralus"

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
```

## <a name="add-resources-to-the-move-collection"></a>Adicionar recursos à coleção de movimentação

Recupere a ID do recurso de origem que você deseja mover. Em seguida, adicione-o à coleção de movimentação.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Saída esperada**

![Texto de saída após recuperar a ID do recurso](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Expected output** 
 Saída ![ esperada Texto de saída depois de adicionar o recurso](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Resolver dependências

Valide os recursos adicionados e resolva quaisquer dependências em outros recursos.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Saída se houver dependências**

Se o recurso na coleção de movimentação tiver dependências em outros recursos, uma mensagem com falha será emitida.

![Texto de saída após a verificação de dependências](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Recuperar dependências

Se o recurso que você deseja mover tiver dependências de outros recursos, você poderá recuperar informações sobre as dependências ausentes.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Saída esperada**

![Texto de saída após recuperar dependências](./media/move-region-powershell/missing-dependencies.png)

Neste exemplo, duas dependências são identificadas como ausentes:

- Grupo de recursos de origem: PSDemoRM
- NIC na VM: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Adicionar dependências à coleção


Usando as IDs de recurso que você recuperou, identifique os nomes dos recursos ausentes e adicione-os à coleção de movimentação.

### <a name="add-the-nic"></a>Como adicionar o NIC

Recupere o nome da NIC e o tipo e adicione-o à coleção.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”
```

**Saída esperada**

![Texto de saída após a recuperação da NIC](./media/move-region-powershell/output-retrieve-nic.png)

Agora, adicione a NIC à coleção move. Este exemplo fornece o mesmo nome para a NIC de destino. Mantenha as configurações e o caso consistentes.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Saída esperada**

![Texto de saída depois de adicionar a NIC à coleção](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>Adicionar o grupo de recursos de origem

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Saída esperada**

![Texto de saída depois de adicionar o grupo de recursos à coleção](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Verificar dependências novamente

Verifique se há dependências ausentes.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Saída**

Há dependências ausentes adicionais.

![Texto de saída depois de verificar novamente as dependências](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>Recuperar dependências adicionais

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Saída esperada**


![Texto de saída após recuperar dependências adicionais](./media/move-region-powershell/additional-missing-dependencies.png)

Neste exemplo, mais três dependências são identificadas como ausentes:

- Endereço IP público: psdemovm-IP
- Rede virtual do Azure: psdemorm-vnet
- NSG (grupo de segurança de rede): psdemovm-NSG

## <a name="add-additional-dependencies"></a>Adicionar dependências adicionais

1. [Siga as instruções](#add-dependencies-to-collection) para adicionar esses recursos à coleção de movimentação.
2. Depois de adicionar recursos, valide novamente até que todas as dependências sejam adicionadas.


## <a name="prepare-and-move-the-source-resource-group"></a>Preparar e mover o grupo de recursos de origem

Na região de origem, você prepara os recursos antes de movê-los. O processo de preparação depende dos recursos que você está movendo. Por exemplo, para recursos sem estado, prepare pode preparar e exportar um modelo de Azure Resource Manager (ARM). Ou, para recursos com estado, a replicação pode ser acionada. 

Antes de poder preparar os recursos de origem, você precisa preparar e mover o grupo de recursos de origem.

### <a name="prepare"></a>Preparar

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Saída esperada**

![Texto de saída depois de preparar o grupo de recursos de origem](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Iniciar movimentação

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Saída esperada**

![Texto de saída depois de iniciar a movimentação do grupo de recursos de origem](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Saída esperada**

![Texto de saída após a confirmação do grupo de recursos de origem](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Preparar recursos

Depois que o grupo de recursos de origem for movido para a região de destino, recupere uma lista dos recursos na coleção de movimentação e prepare-se para movê-los.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “<subscription-id>“ -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Saída esperada**

![Texto de saída após a recuperação de recursos na coleção](./media/move-region-powershell/collection-resources.png)

Agora, prepare os recursos.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
Como alternativa, você pode preparar e mover recursos usando a ID do recurso, em vez do nome:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Saída esperada**

![Texto de saída depois de preparar os recursos na coleção](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Iniciar movimentação de recursos

Depois de preparar os recursos, inicie a movimentação.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Saída esperada**

![Texto de saída após iniciar movimentação de recursos](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>Descartar ou confirmar a movimentação

Após a movimentação inicial, você pode decidir se deseja confirmar a movimentação ou descartá-la. 

- **Descartar**: talvez você descarte uma movimentação se estiver apenas testando e não quiser realmente mover o recurso de origem. Descartar a movimentação retorna o recurso para o estado de *Iniciar movimentação pendente*. Em seguida, você pode iniciar a movimentação novamente, se necessário.
- **Confirmar**: a confirmação conclui a movimentação para a região de destino. Após a confirmação, o recurso de origem estará no estado de *Excluir origem pendente* e você poderá decidir se deseja excluí-lo.

### <a name="discard"></a>Descartar

Para descartar a movimentação:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Saída esperada**

![Texto de saída após descartar a movimentação](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Commit

Para confirmar a movimentação:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Saída esperada**

![Texto de saída após confirmar a movimentação](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Excluir recursos de origem

Depois de confirmar a movimentação e verificar se os recursos funcionam conforme o esperado na região de destino, você pode excluir os recursos de origem no [portal do Azure](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [usando o PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)ou [CLI do Azure](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Próximas etapas

[Saiba como](remove-move-resources.md) remover recursos de uma coleção de movimentação.

---
title: Como gerenciar atribuições com o PowerShell
description: Saiba como gerenciar atribuições de projeto com o módulo PowerShell do Azure BluePrint oficial, Az.Blueprint.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60682825"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Como gerenciar atribuições com o PowerShell

Uma atribuição de planta pode ser gerenciada usando o **Az.Blueprint** módulo PowerShell do Azure. O módulo dá suporte a busca, criando, atualizando e Removendo atribuições. O módulo também pode buscar detalhes nas definições de plano gráfico existente. Este artigo aborda como instalar o módulo e começar a usá-lo.

## <a name="add-the-azblueprint-module"></a>Adicione o módulo Az.Blueprint

Para habilitar o Azure PowerShell para gerenciar atribuições de projeto, o módulo deve ser adicionado. Esse módulo pode ser usado com o PowerShell instalado localmente, com o [Azure Cloud Shell](https://shell.azure.com) ou com a [imagem do Docker do Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Requisitos base

O módulo do Azure BluePrint requer o seguinte software:

- O Azure PowerShell 1.5.0 ou superior. Se ainda não estiver instalado, siga [estas instruções](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 ou superior. Se ele não estiver instalado ou atualizado, siga [estas instruções](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Instalar o módulo

O módulo de especificações técnicas do PowerShell é **Az.Blueprint**.

1. De um prompt **administrativo** do PowerShell, execute o comando a seguir:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Se **Az.Accounts** é já instalado, talvez seja necessário usar `-AllowClobber` para forçar a instalação.

1. Valide que o módulo foi importado e é da versão correta (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Obter definições de plantas

A primeira etapa para trabalhar com uma atribuição é muitas vezes obter uma referência a uma definição de planta.
O `Get-AzBlueprint` cmdlet obtém uma ou mais definições de especificações técnicas. O cmdlet pode obter definições de plantas de um grupo de gerenciamento `-ManagementGroupId {mgId}` ou uma assinatura com `-SubscriptionId {subId}`. O **nome** parâmetro obtém uma definição de planta, mas deve ser usado com **ManagementGroupId** ou **SubscriptionId**. **Versão** pode ser usada com **nome** para ser mais explícito sobre qual definição de planta é retornada. Em vez de **versão**, o comutador `-LatestPublished` captura-as versão publicada mais recentemente.

O exemplo a seguir usa `Get-AzBlueprint` para obter todas as versões de uma definição de plano gráfico chamado ' 101-plantas-definição-subscription' de uma assinatura específica, representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

A saída de exemplo para uma definição de planta com várias versões tem esta aparência:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

O [parâmetros de plano gráfico](../concepts/parameters.md#blueprint-parameters) sobre o plano gráfico definição pode ser expandida para fornecer mais informações.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Obter atribuição de plano gráfico

Se a atribuição de planta já existir, você pode obter uma referência a ele com o `Get-AzBlueprintAssignment` cmdlet. O cmdlet usa **SubscriptionId** e **nome** como parâmetros opcionais. Se **SubscriptionId** não for especificado, o contexto da assinatura atual será usado.

O exemplo a seguir usa `Get-AzBlueprintAssignment` para obter uma atribuição de planta único denominada 'Atribuição-bloqueio-resource-groups' de uma assinatura específica, representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

A saída de exemplo para uma atribuição de planta tem esta aparência:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Criar atribuição de plano gráfico

Se a atribuição de planta ainda não existir, você pode criá-lo com o `New-AzBlueprintAssignment` cmdlet. Esse cmdlet usa os seguintes parâmetros:

- **Nome** [obrigatório]
  - Especifica o nome da atribuição de planta
  - Deve ser exclusivo e não existirem em **SubscriptionId**
- **Plano gráfico** [obrigatório]
  - Especifica a definição de planta atribuir
  - Use `Get-AzBlueprint` para obter o objeto de referência
- **Local** [obrigatório]
  - Especifica a região do atribuído pelo sistema de identidade e assinatura implantação objeto gerenciado a ser criado em
- **Assinatura** (opcional)
  - Especifica a assinatura que a atribuição é implantada em
  - Se não fornecido, o padrão será o contexto da assinatura atual
- **Bloqueio** (opcional)
  - Define o [recurso de bloqueio do plano gráfico](../concepts/resource-locking.md) a ser usado para recursos implantados
  - Opções com suporte: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Se não fornecido, o padrão será _None_
- **SystemAssignedIdentity** (opcional)
  - Selecione para criar uma identidade gerenciada atribuído pelo sistema para a atribuição de e para implantar os recursos
  - Padrão para o conjunto de parâmetros "identity"
  - Não pode ser usado com **UserAssignedIdentity**
- **UserAssignedIdentity** (opcional)
  - Especifica a identidade atribuída pelo usuário gerenciada a ser usado para a atribuição e para implantar os recursos
  - Parte do conjunto de parâmetro "identity"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - Um [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares chave/valor para a configuração [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição de planta
  - O padrão para um parâmetro dinâmico é o **defaultValue** na definição
  - Se um parâmetro não for fornecido e não tem nenhum **defaultValue**, o parâmetro não é opcional

    > [!NOTE]
    > **Parâmetro** não dá suporte a secureStrings.

- **ResourceGroupParameter** (optional)
  - Um [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) dos artefatos do grupo de recursos
  - Cada espaço reservado artefato de grupo de recursos terá uma pares chave/valor para definir dinamicamente **nome** e/ou **local** nesse artefato de grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tem nenhum **defaultValue**, o parâmetro do grupo de recursos não é opcional

O exemplo a seguir cria uma nova atribuição de versão '1.1' da definição de planta 'Meu-plano gráfico' buscada com `Get-AzBlueprint`, define o local de objeto de atribuição e de identidade gerenciado para 'westus2', bloqueios de recursos com  _AllResourcesReadOnly_e define as tabelas de hash para ambos **parâmetro** e **ResourceGroupParameter** sobre assinatura específica, representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

A saída de exemplo para a criação de uma atribuição de planta tem esta aparência:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Atualizar as atribuições de especificações técnicas

Às vezes, é necessário atualizar uma atribuição de planta já foi criada. O `Set-AzBlueprintAssignment` cmdlet lida com essa ação. O cmdlet usa a maior parte os mesmos parâmetros que o `New-AzBlueprintAssignment` cmdlet faz, permitindo que qualquer coisa que foi definido na atribuição a ser atualizada. As exceções a isso são as _nome_, _Blueprint_, e _SubscriptionId_. Somente os valores fornecidos são atualizados.

Para entender o que acontece ao atualizar uma atribuição de planta, consulte [regras para atualizar as atribuições de](./update-existing-assignments.md#rules-for-updating-assignments).

- **Nome** [obrigatório]
  - Especifica o nome da atribuição de plano gráfico para atualizar
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Plano gráfico** [obrigatório]
  - Especifica a definição de planta da atribuição de planta
  - Use `Get-AzBlueprint` para obter o objeto de referência
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Local** (opcional)
  - Especifica a região do atribuído pelo sistema de identidade e assinatura implantação objeto gerenciado a ser criado em
- **Assinatura** (opcional)
  - Especifica a assinatura que a atribuição é implantada em
  - Se não fornecido, o padrão será o contexto da assinatura atual
  - Usado para localizar a atribuição para atualizar, não para alterar a atribuição
- **Bloqueio** (opcional)
  - Define o [recurso de bloqueio do plano gráfico](../concepts/resource-locking.md) a ser usado para recursos implantados
  - Opções com suporte: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (opcional)
  - Selecione para criar uma identidade gerenciada atribuído pelo sistema para a atribuição de e para implantar os recursos
  - Padrão para o conjunto de parâmetros "identity"
  - Não pode ser usado com **UserAssignedIdentity**
- **UserAssignedIdentity** (opcional)
  - Especifica a identidade atribuída pelo usuário gerenciada a ser usado para a atribuição e para implantar os recursos
  - Parte do conjunto de parâmetro "identity"
  - Não pode ser usado com **SystemAssignedIdentity**
- **Parâmetro** (opcional)
  - Um [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) de pares chave/valor para a configuração [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) na atribuição de planta
  - O padrão para um parâmetro dinâmico é o **defaultValue** na definição
  - Se um parâmetro não for fornecido e não tem nenhum **defaultValue**, o parâmetro não é opcional

    > [!NOTE]
    > **Parâmetro** não dá suporte a secureStrings.

- **ResourceGroupParameter** (optional)
  - Um [tabela de hash](/powershell/module/microsoft.powershell.core/about/about_hash_tables) dos artefatos do grupo de recursos
  - Cada espaço reservado artefato de grupo de recursos terá uma pares chave/valor para definir dinamicamente **nome** e/ou **local** nesse artefato de grupo de recursos
  - Se um parâmetro de grupo de recursos não for fornecido e não tem nenhum **defaultValue**, o parâmetro do grupo de recursos não é opcional

O exemplo a seguir atualiza a atribuição da versão '1.1' da definição de planta 'Meu-plano gráfico' buscada com `Get-AzBlueprint` , alterando o modo de bloqueio:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

A saída de exemplo para a criação de uma atribuição de planta tem esta aparência:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Remover atribuição de plano gráfico

Quando é hora de uma atribuição de planta a ser removido, o `Remove-AzBlueprintAssignment` cmdlet lida com essa ação. O cmdlet utiliza um **nome** ou **InputObject** para especificar quais especificações técnicas de atribuição a ser removido. **SubscriptionId** está _necessária_ e deve ser fornecido em todos os casos.

O exemplo a seguir busca uma atribuição de plano gráfico existente com `Get-AzBlueprintAssignment` e, em seguida, ele remove a assinatura específica, representada como `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Exemplo de código de ponta a ponta

Reunir todas as etapas, o exemplo a seguir obtém a definição de planta, em seguida, cria, atualiza e remove a assinatura específica, representada como uma atribuição de planta `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).
---
title: Transferir recursos entre regiões usando o PowerShell no Azure Resource Mover
description: Saiba como transferir recursos entre regiões usando o PowerShell no Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: b728170521570ff0d83b67671109e82adb7fa7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583206"
---
# <a name="move-resources-across-regions-in-powershell"></a>Transferir recursos entre regiões no PowerShell

Este artigo explica como transferir recursos do Azure para uma região do Azure diferente usando o PowerShell no [Azure Resource Mover](overview.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar os pré-requisitos e os requisitos.
> * Configurar a coleção de transferência de recursos.
> * Adicionar recursos à coleção de transferência de recursos e resolver dependências.
> * Preparar e mover o grupo de recursos de origem. 
> * Preparar e mover os outros recursos.
> * Decidir se deseja descartar ou confirmar a movimentação. 
> * Opcionalmente, remover os recursos da região de origem após a movimentação.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usar as opções padrão. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, entre no [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos
**Requisito** | **Descrição**
--- | ---
**Permissões de assinatura** | Verifique se você tem acesso de *Proprietário* na assinatura que contém os recursos que deseja mover<br/><br/> **Por que preciso de acesso de Proprietário?** Na primeira vez que você adicionar um recurso para um par de origem e destino específico em uma assinatura do Azure, o Resource Mover criará uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como MSI [Identidade Gerenciada de Serviço]) que é confiável para a assinatura. Para criar a identidade e atribuir a ela a função necessária (colaborador ou administrador de acesso do usuário na assinatura de origem), a conta usada para adicionar recursos precisa de permissões de *Proprietário* na assinatura. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre as funções do Azure.
**Suporte do Resource Mover** | [Examine](common-questions.md) as regiões com suporte e outras perguntas comuns.
**Suporte à VM** |  Verifique se as VMs que você deseja transferir são compatíveis.<br/><br/> - [Verifique](support-matrix-move-region-azure-vm.md#windows-vm-support) as VMs do Windows compatíveis.<br/><br/> - [Verifique](support-matrix-move-region-azure-vm.md#linux-vm-support) as VMs do Linux e as versões de kernel compatíveis.<br/><br/> - Verifique as configurações de [computação](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) e [rede](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) compatíveis.
**Suporte a SQL** | Se você quiser transferir recursos do SQL, examine a [lista de requisitos do SQL](tutorial-move-region-sql.md#check-sql-requirements).
**Assinatura de destino** | A assinatura na região de destino precisa de cota suficiente para criar os recursos que você está movendo na região de destino. Se não houver cota, [solicite limites adicionais](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Preços da região de destino** | Verifique os preços e os encargos associados à região de destino para a qual você está movendo as VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter ajuda.

### <a name="review-powershell-requirements"></a>Examinar os requisitos do PowerShell

A maioria das operações de transferência de recursos é a mesma para o portal do Azure e para o PowerShell, com algumas exceções.

**Operação** | **PowerShell** | **Portal**
--- | --- | ---
**Criar uma coleção de transferência de recursos** | Uma coleção de transferência de recursos (uma lista de todos os recursos que você está movendo) é criada automaticamente. As permissões de identidade necessárias são atribuídas no back-end pelo portal. | Você usa os cmdlets do PowerShell para:<br/><br/> – Criar um grupo de recursos para a coleção de transferência de recursos e especificar a localização para ele.<br/><br/> – Atribuir uma identidade gerenciada à coleção.<br/><br/> – Adicionar recursos à coleção.
**Remover uma coleção de transferência de recursos** | Não é possível remover diretamente uma coleção de transferência de recursos no portal. | Você usa um cmdlet do PowerShell para remover uma coleção de transferência de recursos.
**Operações de transferência de recursos**<br/><br/> (Prepare, initiate move, commit etc.).| Etapas únicas com validação automática pelo Resource Mover. | Cmdlets do PowerShell para:<br/><br/> 1) Validar dependências.<br/><br/> 2) Realizar a transferência.
**Excluir recursos de origem** | Diretamente no portal do Resource Mover. | Cmdlets do PowerShell no nível de tipo de recurso.


### <a name="sample-values"></a>Valores de exemplo

Estamos usando estes valores em nossos exemplos de script:

**Configuração** | **Valor** 
--- | ---
ID da assinatura | id da assinatura
Região de origem |  Centro dos EUA
Região de destino | Centro-Oeste dos EUA
Grupo de recursos (contendo metadados para a coleção de transferência de recursos) | RG-MoveCollection-demoRMS
Nome da coleção de transferência de recursos | PS-centralus-westcentralus-demoRMS
Grupo de recursos (região de origem) | PSDemoRM 
Grupo de recursos (região de destino) | PSDemoRM-target
Localização do serviço Resource Mover | Leste dos EUA 2
IdentityType | SystemAssigned
VM a ser transferida | PSDemoVM


## <a name="sign-into-azure"></a>Entrar no Azure

Entre na sua assinatura do Azure com o cmdlet Connect-AzAccount:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>Configurar a coleção de transferência de recursos

O objeto MoveCollection armazena metadados e informações de configuração sobre os recursos que você deseja transferir. Para configurar uma coleção de transferência de recursos, faça o seguinte:

- Crie um grupo de recursos para a coleção de transferência de recursos.
- Crie um provedor de serviço para a assinatura, de modo que o recurso MoveCollection possa ser criado.
- Crie o objeto MoveCollection com a identidade gerenciada. Para que o objeto MoveCollection acesse a assinatura na qual o serviço Resource Mover está localizado, ele precisa de uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como MSI (identidade de serviço gerenciada)) que seja confiável para a assinatura.
- Permita à identidade gerenciada acesso à assinatura do Resource Mover.

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Crie um grupo de recursos para os metadados da coleção de transferência de recursos e as informações de configuração, da seguinte maneira:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Saída**:

![Texto de saída após a criação do grupo de recursos](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>Registre o provedor de recursos

1. Registre o provedor de recursos Microsoft.Migrate para que o recurso MoveCollection possa ser criado, conforme demonstrado abaixo:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>Criar um objeto MoveCollection

Crie um objeto MoveCollection e atribua uma identidade gerenciada a ele, da seguinte maneira: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Saída**:

![Texto de saída após criar a coleção de transferência de recursos](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Permitir acesso à identidade gerenciada

Permita à identidade gerenciada acesso à assinatura do Resource Mover, conforme demonstrado a seguir. Você precisa ser o proprietário da assinatura.

1. Recupere detalhes de identidade do objeto MoveCollection.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Atribua as funções necessárias à identidade para que o Azure Resource Mover possa acessar a sua assinatura para ajudar a transferir recursos.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Adicionar recursos à coleção de transferência de recursos

Recupere as IDs para os recursos de origem existentes que você deseja transferir. Crie o objeto de configurações de recurso de destino e adicione recursos à coleção de transferência de recursos.

> [!NOTE]
> Os recursos adicionados a uma coleção de movimentação precisam estar na mesma assinatura, mas podem estar em grupos de recursos diferentes.

Adicione recursos como mostrado abaixo:

1. Obtenha a ID do recurso de origem:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Saída**

    ![Texto de saída após a recuperação da ID do recurso](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Crie o objeto de configurações de recurso de destino de acordo com o recurso que você está movendo. Em nosso caso, esse objeto é uma VM.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Defina o tipo de recurso e o nome do recurso de destino do objeto.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > Nossa VM de destino tem o mesmo nome que a VM na região de origem. Você pode escolher um nome diferente.

4. Adicione os recursos de origem à coleção de transferência de recursos, usando a ID de recurso e o objeto de configurações de destino que você recuperou/criou.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Saída** ![Texto de saída depois de adicionar o recurso](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Validar e adicionar dependências

Verifique se os recursos que você adicionou têm dependências de outros recursos e adicione-as conforme necessário. 

1. Valide as dependências da seguinte maneira:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Saída (quando há dependências)**

    ![Texto de saída após a validação de dependências](./media/tutorial-move-region-powershell/dependency-output.png)

2. Identificar dependências ausentes:

    - Para recuperar uma lista de todas as dependências ausentes:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant"
        ```
        **Saída** ![Texto de saída após a recuperação de uma lista de todas as dependências](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - Para recuperar somente dependências de primeiro nível (dependências diretas do recurso):

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Saída** ![Texto de saída após a recuperação de uma lista de dependências de primeiro nível](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. Para adicionar dependências ausentes pendentes, repita as instruções acima para [adicionar recursos à coleção de transferência de recursos](#add-resources-to-the-move-collection) e revalide até que não haja nenhum recurso pendente.

> [!NOTE]
> Se, por qualquer motivo, você quiser remover recursos da coleção, siga as instruções [neste artigo](remove-move-resources.md).

## <a name="add-the-source-resource-group"></a>Adicionar o grupo de recursos de origem

Adicione o grupo de recursos de origem que contém os recursos que você deseja transferir para a coleção de transferência de recursos.

1. Recupere a ID do grupo de recursos.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Saída** ![Texto de saída após a recuperação da ID do grupo de recursos de origem](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > Estamos usando um grupo de recursos que já está na região de destino.

 
2. Use a ID recuperada para adicionar o grupo de recursos à coleção.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Saída** ![Texto de saída após a adição do grupo de recursos de origem à coleção de transferência de recursos](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Confira as dependências para verificar se você não perdeu nada depois de adicionar o grupo de recursos.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Vemos que não há nenhuma dependência pendente.

    **Saída** ![Texto de saída após a verificação de dependências](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Preparar os recursos

Normalmente, você precisa preparar recursos na região de origem antes da transferência. Por exemplo:

- Para transferir recursos sem estado, como redes virtuais do Azure, adaptadores de rede, balanceadores de carga e grupos de segurança de rede, talvez seja necessário exportar um modelo de Azure Resource Manager.
- Para transferir recursos com estado, como VMs do Azure e bancos de dados SQL, talvez seja necessário iniciar a replicação de recursos da região de origem para a de destino.

Neste tutorial, já que estamos movendo as VMs, para que possamos começar a prepará-las, precisamos primeiro preparar o grupo de recursos de origem e iniciar e confirmar a transferência.

> [!NOTE]
> Se você tiver um grupo de recursos de destino existente, poderá confirmar diretamente a transferência do grupo de recursos de origem e ignorar os estágios preparar e iniciar a transferência.

  
### <a name="prepare-the-source-resource-group"></a>Prepare o grupo de recursos de origem:

1. Prepare o grupo de recursos:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Saída**

    ![Texto de saída depois de preparar o grupo de recursos de origem](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Inicie a transferência do grupo de recursos de origem.

    ```azurepowershell-interactive
    "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![Texto de saída após o início da transferência do grupo de recursos de origem](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Confirme a transferência do grupo de recursos de origem.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Saída**

    ![Texto de saída após a confirmação do grupo de recursos de origem](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>Preparar os recursos da VM

Depois de preparar e transferir o grupo de recursos de origem, podemos preparar os recursos da VM para a transferência.

1. Valide as dependências antes de preparar os recursos da VM.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Saída**

    ![Texto de saída após a validação da VM antes de prepará-la](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. Obtenha os recursos dependentes que precisam ser preparados junto com a VM.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Saída**

    ![Texto de saída após a recuperação de recursos de VM dependentes](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Inicie o processo de preparação para todos os recursos dependentes.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Saída**

    ![Texto de saída após o início da preparação de todos os recursos](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > Você pode fornecer a ID do recurso de origem em vez do nome do recurso como os parâmetros de entrada para o cmdlet Prepare, bem como nos cmdlets Initiate Move e Commit. Para fazer isso, execute:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>Iniciar a transferência de recursos da VM

1. Verifique se os recursos da VM estão em um estado *Iniciar transferência pendente*:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Saída**

    ![Texto de saída após a verificação do estado iniciar](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. Inicie a transferência:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Saída**

    ![Texto de saída após o início da transferência de recursos](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Descartar ou confirmar?

Após a movimentação inicial, você pode decidir se deseja confirmar a movimentação ou descartá-la. 

- **Descartar**: talvez você descarte uma movimentação se estiver apenas testando e não quiser realmente mover o recurso de origem. Descartar a movimentação retorna o recurso para o estado de *Iniciar movimentação pendente*. Em seguida, você pode iniciar a transferência novamente, se necessário.
- **Confirmar**: a confirmação conclui a movimentação para a região de destino. Após a confirmação, o recurso de origem estará no estado de *Excluir origem pendente* e você poderá decidir se deseja excluí-lo.

### <a name="discard-the-move"></a>Descartar a movimentação

Para descartar a transferência:

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Saída**

![Texto de saída após o descarte da transferência](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>Confirmar a movimentação

1. Confirme a transferência da seguinte maneira:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Saída**

    ![Texto de saída após a confirmação da transferência](./media/tutorial-move-region-powershell/commit-move.png)

2. Verifique se todos os recursos foram movidos para a região de destino:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Todos os recursos agora estão em um estado de *Exclusão de origem pendente* na região de destino.

## <a name="delete-source-resources"></a>Excluir recursos de origem

Depois de confirmar a transferência e verificar se os recursos funcionam conforme o esperado na região de destino, você pode excluir cada recurso de origem no [portal do Azure](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [usando o PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources) ou na [CLI do Azure](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Moveu VMs do Azure para outra região do Azure usando o PowerShell.
> * Moveu recursos associados às VMs para outra região.

Agora, tentando mover as VMs do Azure usando o portal

> [!div class="nextstepaction"]
> [Transferir as VMs do Azure no portal](./tutorial-move-region-virtual-machines.md)



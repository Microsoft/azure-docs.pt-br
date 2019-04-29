---
title: Modificar um conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como modificar e atualizar um conjunto de dimensionamento de máquinas virtuais do Azure com as APIs REST, Azure PowerShell e CLI do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: manayar
ms.openlocfilehash: 71899a9d6782c4700c287458c85ec83bd1516a4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803142"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificar um conjunto de dimensionamento de máquinas virtuais

Em todo o ciclo de vida de seus aplicativos, talvez seja necessário modificar ou atualizar o conjunto de escala de máquina virtual. Essas atualizações podem incluir como atualizar a configuração do conjunto de escala, ou alterar a configuração do aplicativo. Este artigo descreve como modificar um conjunto de dimensionamento com as APIs REST, o Azure PowerShell ou o CLI do Azure.

## <a name="fundamental-concepts"></a>Conceitos fundamentais

### <a name="the-scale-set-model"></a>O modelo do conjunto de dimensionamento
Um conjunto de dimensionamento tem um "modelo de conjunto de dimensionamento" que captura o estado *desejado* do conjunto de dimensionamento como um todo. Para consultar o modelo de um conjunto de dimensionamento, você pode 

- a API REST com [compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) da seguinte maneira:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- CLI do Azure com [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Você também pode usar [resources.azure.com](https://resources.azure.com) ou específicos do idioma [Azure SDKs](https://azure.microsoft.com/downloads/).

A apresentação exata da saída depende das opções que você fornecer para o comando. O exemplo a seguir mostra a saída de exemplo condensada da CLI do Azure:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Essas propriedades se aplicam ao conjunto de dimensionamento como um todo.


### <a name="the-scale-set-instance-view"></a>A exibição de instância do conjunto de dimensionamento
Um conjunto de dimensionamento também tem uma "exibição de instância do conjunto de dimensionamento" que captura o estado atual do *tempo de execução* do conjunto de dimensionamento como um todo. Para consultar a exibição de instância de um conjunto de dimensionamento, você pode usar:

- a API REST com [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) da seguinte maneira:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- CLI do Azure com [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Você também pode usar [resources.azure.com](https://resources.azure.com) ou específicos do idioma [Azure SDKs](https://azure.microsoft.com/downloads/)

A apresentação exata da saída depende das opções que você fornecer para o comando. O exemplo a seguir mostra a saída de exemplo condensada da CLI do Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Essas propriedades fornecem um resumo do estado atual de tempo de execução das VMs no conjunto de dimensionamento, como o status das extensões aplicadas ao conjunto de dimensionamento.


### <a name="the-scale-set-vm-model-view"></a>A exibição do modelo de VM do conjunto de dimensionamento
Semelhante a como um conjunto de dimensionamento tem uma exibição de modelo, cada instância de VM no conjunto de dimensionamento tem sua própria exibição de modelo. Para consultar a exibição do modelo para uma determinada instância de máquina virtual em um conjunto de dimensionamento, você pode usar:

- a API REST com [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) da seguinte maneira:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- CLI do Azure com [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Você também pode usar [resources.azure.com](https://resources.azure.com) ou [Azure SDKs](https://azure.microsoft.com/downloads/).

A apresentação exata da saída depende das opções que você fornecer para o comando. O exemplo a seguir mostra a saída de exemplo condensada da CLI do Azure:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Essas propriedades descrevem a configuração de uma instância de VM dentro de um conjunto de escalas, não a configuração do conjunto de escalas como um todo. Por exemplo, o modelo de conjunto de escala tem `overprovision` como uma propriedade, enquanto o modelo para uma instância de VM dentro de um conjunto de escala não tem. Essa diferença se dá porque o provisionamento em excesso é uma propriedade para o conjunto de dimensionamento como um todo e não para instâncias de VMs individuais no conjunto de dimensionamento (para obter mais informações sobre provisionamento em excesso, consulte [as considerações de Design para conjuntos de dimensionamento](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>A exibição de instância da VM do conjunto de dimensionamento
Semelhante a como um conjunto de dimensionamento tem uma exibição de instância, cada instância de VM no conjunto de dimensionamento tem sua própria exibição de instância. Para consultar a visualização da instância de uma determinada instância da VM em um conjunto de escalas, você pode usar:

- a API REST com [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) da seguinte maneira:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell com [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- CLI do Azure com [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Você também pode usar [resources.azure.com](https://resources.azure.com) ou [Azure SDKs](https://azure.microsoft.com/downloads/)

A apresentação exata da saída depende das opções que você fornecer para o comando. O exemplo a seguir mostra a saída de exemplo condensada da CLI do Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Essas propriedades descrevem o estado de tempo de execução atual de uma instância de VM dentro de um conjunto de escala, que inclui todas as extensões aplicadas ao conjunto de escala.


## <a name="how-to-update-global-scale-set-properties"></a>Como atualizar propriedades globais do conjunto de dimensionamento
Para atualizar uma propriedade global do conjunto de dimensionamento, você deve atualizar a propriedade no modelo do conjunto de dimensionamento. Você pode fazer essa atualização por meio de:

- a API REST com [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) da seguinte maneira:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Como alternativa, um modelo do Gerenciador de Recursos com as propriedades da API REST para atualizar as propriedades do conjunto de dimensionamento global.

- Azure PowerShell com [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- CLI do Azure com [az vmss update](/cli/azure/vmss):
    - Para modificar uma propriedade:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Para adicionar um objeto a uma propriedade de lista em um conjunto de dimensionamento: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Para remover um objeto de uma propriedade de lista em um conjunto de dimensionamento: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Se você tiver implantado anteriormente o conjunto de dimensionamento com o comando `az vmss create`, você pode executar o comando `az vmss create` novamente para atualizar o conjunto de dimensionamento. Certifique-se de que todas as propriedades do comando `az vmss create` sejam as mesmas de antes, exceto as propriedades que você quer modificar.

- Você também pode usar [resources.azure.com](https://resources.azure.com) ou [Azure SDKs](https://azure.microsoft.com/downloads/).

Depois que o modelo de conjunto de dimensionamento for atualizado, a nova configuração se aplicará a novas VMs criadas no conjunto de dimensionamento. No entanto, os modelos para VMs existentes no conjunto de dimensionamento ainda devem ser atualizados com o modelo geral mais recente do conjunto de dimensionamento. No modelo para cada VM, há uma propriedade booleana chamada `latestModelApplied` que indica se a VM está atualizada com o modelo geral mais recente do conjunto de dimensionamento ou não (`true` significa que a VM está atualizada com o modelo mais recente).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Como atualizar as VMs com o modelo mais recente do conjunto de dimensionamento
Os conjuntos de dimensionamento têm uma "política de atualização" que determinam como as VMs são atualizadas com o modelo mais recente do conjunto de dimensionamento. Os três modos da política de atualização são:

- **Automático** - neste modo, o conjunto de escala não garante a ordem das VMs sendo interrompidas. O conjunto de dimensionamento pode interromper todas as VMs ao mesmo tempo. 
- **Distribuição em lotes** - neste modo, o conjunto de dimensionamento distribui a atualização em lotes com um tempo de pausa opcional entre os lotes.
- **Manual** - neste modo, quando você atualiza o modelo do conjunto de dimensionamento, nada acontecerá nas VMs existentes.
 
Para atualizar VMs existentes, você deve fazer uma "atualização manual" de cada VM existente. Você pode fazer essa atualização manual com:

- a API REST com [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) da seguinte maneira:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell com [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- CLI do Azure com [az vmss update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Você também pode usar [Azure SDKs](https://azure.microsoft.com/downloads/) específicos do idioma.

>[!NOTE]
> Clusters do Service Fabric só podem usar o modo *Automático*, mas a atualização é tratada de maneira diferente. Para obter mais informações, consulte [atualizações de aplicativo do Service Fabric](../service-fabric/service-fabric-application-upgrade.md).

Há um tipo de modificação das propriedades globais do conjunto de dimensionamento que não segue a política de atualização. Altera para o conjunto de dimensionamento de perfil de sistema operacional (como o nome de usuário administrador e senha) só pode ser alterado na versão da API *2017-12-01* ou posterior. Essas alterações se aplicam somente a VMs criadas depois da alteração no modelo do conjunto de dimensionamento. Para atualizar VMs existentes, você deve "refazer a imagem" de cada VM existente. Você pode refazer a imagem por meio de:

- a API REST com [compute/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage) da seguinte maneira:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell com [Set-AzVmssVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- CLI do Azure com [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Você também pode usar [Azure SDKs](https://azure.microsoft.com/downloads/) específicos do idioma.


## <a name="properties-with-restrictions-on-modification"></a>Propriedades com restrições de modificação

### <a name="create-time-properties"></a>Propriedades do momento da criação
Algumas propriedades só podem ser definidas ao criar o conjunto de dimensionamento. Essas propriedades incluem:

- Zonas de Disponibilidades
- Publicador de referência de imagem
- Oferta de referência de imagem
- Tipo de conta de armazenamento em disco de SO gerenciado

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Propriedades que só podem ser alteradas com base no valor atual
Algumas propriedades podem ser alteradas, com exceções dependendo do valor atual. Essas propriedades incluem:

- **singlePlacementGroup** - se singlePlacementGroup for true, ele pode ser modificado para false. No entanto, se singlePlacementGroup for false, ele **não poderá** ser modificado para true.
- **sub-rede** - a sub-rede de um conjunto de dimensionamento pode ser modificada desde que a sub-rede original e a nova sub-rede estejam na mesma rede virtual.

### <a name="properties-that-require-deallocation-to-change"></a>Propriedades que exigem desalocação para serem alteradas
Algumas propriedades só podem ser alteradas para determinados valores se as VMs no conjunto de dimensionamento forem desalocadas. Essas propriedades incluem:

- **Nome do SKU**- se não houver suporte para a nova SKU de VM no hardware em que o conjunto de dimensionamento está no momento, você precisará desalocar as VMs no conjunto de dimensionamento antes de modificar o nome do SKU. Para saber mais, confira [Como monitorar uma Azure VM](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Atualizações específicas da VM
Algumas modificações podem ser aplicadas a VMs específicas em vez das propriedades globais do conjunto de dimensionamento. Atualmente, a única atualização específica de VM compatível é anexação/desanexação de discos de dados para/de VMs no pacote de atualização. Esse recurso está em visualização. Para saber mais, confira a [documentação de versão prévia](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Cenários

### <a name="application-updates"></a>Atualizações de aplicativos
Se um aplicativo for implantado em uma conjunto de dimensionamento por meio de extensões, uma atualização da configuração da extensão fará com que o aplicativo atualize de acordo com a política de atualização. Por exemplo, se você tiver uma nova versão de um script para ser executado em uma extensão de script personalizada, você pode atualizar a propriedade *fileUris* para apontar para o novo script. Em alguns casos, talvez você queira forçar uma atualização mesmo que a configuração da extensão permaneça inalterada (por exemplo, você atualizou o script sem alterar o URI do script). Nesses casos, você pode modificar o *forceUpdateTag* para forçar uma atualização. A plataforma Windows Azure não interpreta essa propriedade. Se você alterar o valor, não há nenhum efeito sobre como a extensão é executada. Uma mudança simplesmente força a extensão para ser executada novamente. Para obter mais informações sobre o *forceUpdateTag*, consulte a documentação da [API REST para extensões](/rest/api/compute/virtualmachineextensions/createorupdate). Observe que *forceUpdateTag* pode ser usado com todas as extensões, não apenas com a extensão de script personalizada.

Também é comum que aplicativos sejam implantados por meio de uma imagem personalizada. Esse cenário é abordado na seção a seguir.

### <a name="os-updates"></a>Atualizações de SO
Se você estiver usando imagens de plataforma do Azure, poderá atualizar a imagem modificando o *imageReference* (mais informações na documentação da [API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Com imagens de plataforma, é comum especificar "mais recente" para a versão de referência de imagem. Ao criar, dimensionar e recriar a imagem, as VMs são criadas com a versão disponível mais recente. No entanto, isso **não** significa que a imagem do SO será atualizada automaticamente ao longo do tempo com o lançamento de novas versões da imagem. Um recurso separado está atualmente em visualização que fornece atualizações automáticas do OS. Para obter mais informações, confira a [Documentação de Atualização Automática do SO](virtual-machine-scale-sets-automatic-upgrade.md).

Se você usar imagens personalizadas, poderá atualizar a imagem atualizando a ID do *imageReference* (mais informações na documentação da [API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exemplos

### <a name="update-the-os-image-for-your-scale-set"></a>Atualizar a imagem do SO para seu conjunto de dimensionamento
Você pode ter um conjunto de dimensionamento executando uma versão antiga do Ubuntu LTS 16.04. Você deseja atualizar para uma versão mais recente do Ubuntu LTS 16.04, como a versão *16.04.201801090*. A propriedade da versão de referência da imagem não é parte de uma lista, logo você pode modificar diretamente essas propriedades com um desses seguintes comandos:

- Azure PowerShell com [Update-AzVmss](/powershell/module/az.compute/update-azvmss) como a seguir:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- CLI do Azure com [az vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Como alternativa, você talvez queira alterar a imagem que seu conjunto de dimensionamento usa. Por exemplo, você talvez queira atualizar ou alterar uma imagem personalizada usada pelo seu conjunto de dimensionamento. Você pode alterar a imagem que seu conjunto de dimensionamento usa atualizando a propriedade da ID de referência da imagem. A propriedade da ID de referência da imagem não é parte de uma lista, logo você pode modificar diretamente essa propriedade com um destes seguintes comandos:

- Azure PowerShell com [Update-AzVmss](/powershell/module/az.compute/update-azvmss) como a seguir:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- CLI do Azure com [az vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Atualizar o balanceador de carga para o conjunto de dimensionamento
Digamos que você tenha um conjunto de dimensionamento com um Azure Load Balancer e você queira substituir o Azure Load Balancer por um Gateway de Aplicativo do Azure. As propriedades do balanceador de carga e do gateway do aplicativo referentes a um conjunto de dimensionamento são parte de uma lista, logo você pode usar os comandos para remover e adicionar elementos da lista em vez de modificar as propriedades diretamente:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- CLI do Azure:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Esses comandos pressupõem que há apenas um balanceador de carga e uma configuração de IP no conjunto de dimensionamento. Se houver vários, você precisará usar um índice de lista diferente de *0*.


## <a name="next-steps"></a>Próximas etapas
Também é possível executar tarefas comuns de gerenciamento em conjuntos de dimensionamento com a [CLI do Azure](virtual-machine-scale-sets-manage-cli.md) ou [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).

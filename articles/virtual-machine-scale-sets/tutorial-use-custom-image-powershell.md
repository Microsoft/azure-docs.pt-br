---
title: Tutorial – Usar uma imagem de VM personalizada em um conjunto de dimensionamento com o Azure PowerShell
description: Aprenda a usar o Azure PowerShell para criar uma imagem de VM personalizada que pode ser usada para implantar um conjunto de dimensionamento de máquinas virtuais
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 803bbf485d783fc160d1f2644a56e1339f73690e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678298"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Tutorial: Criar e usar discos uma imagem personalizada para conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell

Ao criar um conjunto de dimensionamento, você especifica uma imagem a ser usada quando as instâncias de VM forem implantadas. Para reduzir a quantidade de tarefas depois que as instâncias de VM forem implantadas, é possível usar uma imagem de VM personalizada. Esta imagem de VM personalizada inclui todas as instalações ou configurações de aplicativo necessárias. Todas as instâncias de VM criadas no conjunto de dimensionamento usam a imagem de VM personalizada e estão prontas para atender ao tráfego do aplicativo. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar uma Galeria de Imagens Compartilhadas
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar um conjunto de dimensionamento com base em uma imagem 
> * Compartilhar uma galeria de imagens

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

As etapas abaixo detalham como transformar uma VM existente em uma imagem personalizada reutilizável que você pode usar para criar um conjunto de dimensionamento.

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, confira o [Início rápido do PowerShell](quick-create-powershell.md) para criar uma VM a ser usada neste tutorial. Ao trabalhar no tutorial, substitua os nomes dos recursos se necessário.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.


## <a name="get-the-vm"></a>Obter a VM

Você pode ver uma lista das VMss que estão disponíveis em um grupo de recursos usando [Get-AzVM](/powershell/module/az.compute/get-azvm). Depois que souber o nome da VM e em qual grupo de recursos ela está, você poderá usar `Get-AzVM` novamente para obter o objeto da VM e armazená-lo em uma variável para uso posterior. Este exemplo obtém uma VM chamada *sourceVM* do grupo de recursos "myResourceGroup" e a atribui à variável *$vm*. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. No seguinte exemplo, um grupo de recursos chamado *myGalleryRG* é criado na região *EastUS*:

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. Os caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da galeria não pode conter traços. Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

Crie uma galeria de imagens usando [New-AzGallery](/powershell/module/az.compute/new-azgallery). O exemplo a seguir cria uma galeria chamada *myGallery* no grupo de recursos *myGalleryRG*.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Criar uma definição de imagem 

As definições de imagem criam um agrupamento lógico para as imagens. Elas são usadas para gerenciar informações sobre as versões da imagem que são criadas dentro delas. Os nomes das definições de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos finais. Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, confira [Definições de imagem](../virtual-machines/shared-image-galleries.md#image-definitions).

Crie a definição de imagem usando [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). Neste exemplo, a imagem da galeria se chama *myGalleryImage* e foi criada para uma imagem especializada. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Criar uma versão de imagem

Crie uma versão da imagem com base em uma VM usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Caracteres permitidos para a versão da imagem são números e pontos. Os números devem estar dentro do intervalo de um inteiro de 32 bits. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

Neste exemplo, a versão da imagem é *1.0.0* e ela é replicada para os datacenters *Leste dos EUA* e *Centro-Sul dos EUA*. Ao escolher as regiões de destino da replicação, você precisa incluir a região de *origem* como um destino para a replicação.

Para criar uma versão da imagem da VM, use `$vm.Id.ToString()` para o `-Source`.

```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='East US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Pode levar algum tempo para replicar a imagem para todas as regiões de destino.

## <a name="create-a-scale-set-from-the-image"></a>Criar um conjunto de dimensionamento com base na imagem
Agora crie um conjunto de dimensionamento usando [New-AzVmss](/powershell/module/az.compute/new-azvmss), que usa o parâmetro `-ImageName` para definir a imagem de VM personalizada criada na etapa anterior. Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, além de permitir o tráfego de área de trabalho remota na porta TCP 3389 e comunicação remota do PowerShell na porta TCP 5985. Quando solicitado, forneça suas próprias credenciais administrativas desejadas para as instâncias de VM no conjunto de dimensionamento:

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $galleryImage.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig 

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="share-the-gallery"></a>Compartilhar a galeria

Recomendamos que você compartilhe o acesso no nível da galeria de imagens. Use um endereço de email e o cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) para obter a ID do objeto do usuário e, em seguida, use [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) para conceder a ele acesso à galeria. Substitua o email de exemplo, alinne_montes@contoso.com neste exemplo, por suas informações.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados:

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Construtor de Imagens do Azure

O Azure também oferece um serviço integrado ao Packer, o [Construtor de Imagens de VM do Azure](../virtual-machines/image-builder-overview.md). Basta descrever suas personalizações em um modelo e ele cuidará da criação da imagem. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar e usar uma imagem de VM personalizada para seus conjuntos de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Criar uma Galeria de Imagens Compartilhadas
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar um conjunto de dimensionamento com base em uma imagem 
> * Compartilhar uma galeria de imagens

Siga para o próximo tutorial para saber como implantar aplicativo ao seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Implantar aplicativos em seus conjuntos de dimensionamento](tutorial-install-apps-powershell.md)
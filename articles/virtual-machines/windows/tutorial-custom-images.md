---
title: Tutorial – Criar imagens personalizadas de VM com o Azure PowerShell
description: Neste tutorial, você aprende a usar o Azure PowerShell para criar uma imagem de máquina virtual personalizada no do Windows armazenada em uma Galeria de Imagens Compartilhadas do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 145b7300272243099cfccad0c99d8f69e83e6762
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555815"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Tutorial: Criar imagens de VM do Windows com o Azure PowerShell

Imagens podem ser usadas para inicializar implantações e garantir a consistência entre várias VMs. Neste tutorial, você cria uma imagem especializada de uma máquina virtual do Azure usando o PowerShell e a armazena em uma Galeria de Imagens Compartilhadas. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma Galeria de Imagens Compartilhadas
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar uma VM de uma imagem 
> * Compartilhar uma galeria de imagens



## <a name="before-you-begin"></a>Antes de começar

As etapas abaixo detalham como transformar uma VM existente em uma imagem personalizada reutilizável que você pode usar para criar VMs.

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, confira o [Início rápido do PowerShell](quick-create-powershell.md) para criar uma VM a ser usada neste tutorial. Ao trabalhar no tutorial, substitua os nomes dos recursos se necessário.

## <a name="overview"></a>Visão geral

Uma [Galeria de Imagens Compartilhadas](../shared-image-galleries.md) simplifica o compartilhamento da imagem personalizada em sua organização. Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. 

A Galeria de Imagens Compartilhadas permite que você compartilhe suas imagens de VM personalizadas com outras pessoas. Escolha quais imagens você deseja compartilhar, em quais regiões deseja torná-las disponíveis e com quem deseja compartilhá-las. 

O recurso Galeria de Imagens Compartilhadas tem vários tipos de recursos:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="get-the-vm"></a>Obter a VM

Você pode ver uma lista das VMss que estão disponíveis em um grupo de recursos usando [Get-AzVM](/powershell/module/az.compute/get-azvm). Depois que souber o nome da VM e em qual grupo de recursos ela está, você poderá usar `Get-AzVM` novamente para obter o objeto da VM e armazená-lo em uma variável para uso posterior. Este exemplo obtém uma VM chamada *sourceVM* do grupo de recursos "myResourceGroup" e a atribui à variável *$sourceVM*. 

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

As definições de imagem criam um agrupamento lógico para as imagens. Elas são usadas para gerenciar informações sobre as versões da imagem que são criadas dentro delas. Os nomes das definições de imagem podem ser compostos por letras maiúsculas ou minúsculas, dígitos, pontos, traços e pontos finais. Para obter mais informações sobre os valores que pode especificar para uma definição de imagem, confira [Definições de imagem](../shared-image-galleries.md#image-definitions).

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


## <a name="create-a-vm"></a>Criar uma máquina virtual 

Agora que tem uma imagem especializada, você pode criar uma ou mais VMs. Usando o cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm). Para usar a imagem, use `Set-AzVMSourceImage` e defina o `-Id` como a ID da definição de imagem ($galleryImage.ID, nesse caso) para sempre usar a versão mais recente da imagem. 

Substitua os nomes dos recursos conforme necessário no exemplo. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


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

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Construtor de Imagens do Azure

O Azure também oferece um serviço integrado ao Packer, o [Construtor de Imagens de VM do Azure](../image-builder-overview.md). Basta descrever suas personalizações em um modelo e ele cuidará da criação da imagem. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de VM especializada. Você aprendeu a:

> [!div class="checklist"]
> * Criar uma Galeria de Imagens Compartilhadas
> * Criar uma definição de imagem
> * Criar uma versão de imagem
> * Criar uma VM de uma imagem 
> * Compartilhar uma galeria de imagens

Avance para o próximo tutorial para aprender como criar máquinas virtuais altamente disponíveis.

> [!div class="nextstepaction"]
> [Criar VMs altamente disponíveis](tutorial-availability-sets.md)
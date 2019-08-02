---
title: Tutorial – Criar imagens personalizadas de VM com o Azure PowerShell | Microsoft Docs
description: Neste tutorial, você aprenderá a usar o Azure PowerShell para criar uma imagem de máquina virtual personalizada no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fe19ea2d8946d645704139bbf2faa80f21e84039
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708057"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Tutorial: Criar uma imagem personalizada de uma VM do Azure com o Azure PowerShell

Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. Imagens personalizadas podem ser usadas para inicializar implantações e garantir a consistência entre várias VMs. Neste tutorial, você criará sua própria imagem personalizada de uma máquina virtual do Azure usando o PowerShell. Você aprenderá como:

> [!div class="checklist"]
> * Aplicar Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM por meio de uma imagem personalizada
> * Listar todas as imagens na sua assinatura
> * Excluir uma imagem

## <a name="before-you-begin"></a>Antes de começar

As etapas abaixo detalham como pegar uma máquina virtual existente e transformá-la em uma imagem personalizada reutilizável que você pode usar para criar novas instâncias de VM.

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, este [exemplo de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar uma para você. Ao trabalhar com este tutorial, substitua o grupo de recursos e os nomes de VM onde for necessário.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="prepare-vm"></a>Preparar VM

Para criar uma imagem de uma máquina virtual, é necessário preparar a VM de origem generalizando-a, desalocando-a e, em seguida, marcando-a como generalizada no Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM Windows usando Sysprep

O Sysprep remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, confira [Como usar o Sysprep: Um introdução](https://technet.microsoft.com/library/bb457073.aspx).


1. Conectar-se à máquina virtual.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para *%windir%\system32\sysprep* e, a seguir, execute`sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.
4. Em **Opções de Desligamento**, selecione **Desligar** e em **OK**.
5. Quando o Sysprep for concluído, desligará a máquina virtual. **Não reinicie a VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desalocar e marcar a VM como generalizada

Para criar uma imagem, a VM precisa ser desalocada e marcada como generalizada no Azure.

Desaloque a VM usando [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Defina o status da máquina virtual como `-Generalized` usando [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Criar a imagem

Agora, é possível criar uma imagem da VM usando [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) e [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). O exemplo a seguir cria uma imagem chamada *myImage* por meio de uma VM chamada *myVM*.

Obtenha a máquina virtual. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Crie a configuração de imagem.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Crie a imagem.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Criar VMs por meio da imagem

Agora que tem uma imagem, você pode criar uma ou mais VMs novas por meio da imagem. A criação de uma VM com base em uma imagem personalizada é semelhante à criação de uma VM usando uma imagem do Marketplace. Ao usar uma imagem do Marketplace, você precisa fornecer informações sobre a imagem, o provedor da imagem, a oferta, a SKU e a versão. Ao usar o parâmetro simplificado definido para o cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), é necessário apenas fornecer o nome da imagem personalizada, desde que ela esteja no mesmo grupo de recursos. 

Este exemplo cria uma VM chamada *myVMfromImage* com base na imagem *myImage* em *myResourceGroup*.


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>Gerenciamento de imagens 

Estes são alguns exemplos de tarefas comuns de imagem gerenciada e como para concluí-las usando o PowerShell.

Liste todas as imagens por nome.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Exclua uma imagem. Este exemplo exclui a imagem denominada *myImage* do *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de VM personalizada. Você aprendeu como:

> [!div class="checklist"]
> * Aplicar Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM por meio de uma imagem personalizada
> * Listar todas as imagens na sua assinatura
> * Excluir uma imagem

Avance para o próximo tutorial para aprender como criar máquinas virtuais altamente disponíveis.

> [!div class="nextstepaction"]
> [Criar VMs altamente disponíveis](tutorial-availability-sets.md)




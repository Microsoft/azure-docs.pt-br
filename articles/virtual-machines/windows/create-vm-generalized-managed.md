---
title: Criar uma VM de uma imagem gerenciada no Azure
description: Crie uma máquina virtual do Windows de uma imagem de gerenciada generalizada usando o Azure PowerShell ou o portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: bc87b0ec9cf41b2ddc5746a9734463f544b0cc2a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550681"
---
# <a name="create-a-vm-from-a-managed-image"></a>Criar uma VM por meio de uma imagem gerenciada

Você pode criar várias VMs (máquinas virtuais) de uma imagem de VM gerenciada do Azure usando o PowerShell ou o portal do Azure. Uma imagem de VM gerenciada contém as informações necessárias para criar uma VM, incluindo o sistema operacional e os discos de dados. Os VHDs (discos rígidos virtuais) que formam a imagem, incluindo os discos do sistema operacional e quaisquer discos de dados, são armazenados como discos gerenciados. 

Antes de criar uma VM, será necessário [criar uma imagem de VM gerenciada](capture-image-resource.md) para usar como a imagem de origem e conceder acesso de leitura na imagem a qualquer usuário que deva ter acesso a ela. 

Uma imagem gerenciada dá suporte a até 20 implantações simultâneas. A tentativa de criar simultaneamente mais de 20 VMs a partir da mesma imagem gerenciada pode exceder os tempos limite de provisionamento devido às limitações de desempenho de armazenamento de um único VHD. Para criar simultaneamente mais de 20 VMs, use uma imagem das [Galerias de Imagens Compartilhadas](../shared-image-galleries.md), configurada com uma réplica para cada 20 implantações simultâneas de VM.

## <a name="use-the-portal"></a>Usar o portal

1. Acesse o [portal do Azure](https://portal.azure.com) para localizar uma imagem gerenciada. Pesquise e selecione **Imagens**.
3. Selecione a imagem que você deseja usar a partir da lista. A imagem da página **Visão geral** será aberta.
4. Clique em **Criar VM** no menu.
5. Insira as informações da máquina virtual. O nome do usuário e a senha inseridos aqui serão usados para fazer logon na máquina virtual. Quando concluir, selecione **OK**. Você pode criar a nova VM em um grupo de recursos existente ou escolher **Criar novo** para criar um novo grupo de recursos para armazenar a VM.
6. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. 
7. Em **Configurações**, faça as alterações necessárias e selecione **OK**. 
8. Na página de resumo, você deve ver o nome da sua imagem listado como uma **Imagem privada**. Selecione **OK** para iniciar a implantação da máquina virtual.


## <a name="use-powershell"></a>Usar o PowerShell

Você pode usar o PowerShell para criar uma VM por meio de uma imagem usando o conjunto de parâmetro simplificado definido para o novo cmdlet do [New-AzVm](/powershell/module/az.compute/new-azvm). A imagem precisa estar no mesmo grupo de recursos no qual você criará a VM.

 

O parâmetro simplificado definido para [New-AzVm](/powershell/module/az.compute/new-azvm) requer apenas que você forneça um nome, um grupo de recursos e o nome da imagem para criar uma VM de uma imagem. New-AzVm usará o valor do parâmetro **-Name** como o nome de todos os recursos que cria automaticamente. Neste exemplo, fornecemos nomes mais detalhados para cada recurso, mas permitimos ao cmdlet criá-los automaticamente. Você também pode criar recursos, como a rede virtual, antecipadamente e passar o nome do recurso para o cmdlet. New-AzVm usará os recursos existentes se puder encontrá-los pelo nome.

O exemplo a seguir cria uma máquina virtual chamada *myVMFromImage*, no grupo de recursos *myResourceGroup*, na imagem chamada *myImage*. 


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



## <a name="next-steps"></a>Próximas etapas
[Criar e gerenciar VMs do Windows com o módulo do Azure PowerShell](tutorial-manage-vm.md)
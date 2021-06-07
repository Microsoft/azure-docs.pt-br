---
title: Criar uma VM com um endereço IP público estático - PowerShell | Microsoft Docs
description: Crie uma VM (máquina virtual) com um endereço IP público estático usando o PowerShell. Endereços IP públicos estáticos são endereços que nunca mudam.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 047a6db82e65c97deba5270d181f72315a67e82c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791249"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>Crie uma máquina virtual com um endereço IP público estático usando o PowerShell


Você pode criar uma máquina virtual com um endereço IP público estático. Um endereço IP público permite que você se comunique com uma máquina virtual pela Internet. Atribua um endereço IP público estático, em vez de um endereço dinâmico, para garantir que o endereço nunca seja alterado. Saiba mais sobre os [endereços IP públicos estáticos](./public-ip-addresses.md#allocation-method). Para alterar um endereço IP público atribuído a uma máquina virtual existente de dinâmico para estático, ou para trabalhar com endereços IP, consulte [adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Endereços IP públicos têm [carga nominal](https://azure.microsoft.com/pricing/details/ip-addresses), e há um [limite](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para o número de endereços IP públicos que você pode usar por assinatura.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Você pode concluir as etapas a seguir no seu computador local ou usando o Shell de Nuvem do Azure. Para usar seu computador local, verifique se você tem o [Azure PowerShell instalado](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json). Para usar o Azure Cloud Shell, selecione **Experimente** no canto superior direito de qualquer caixa de comando a seguir. O Cloud Shell entrará no Azure.

1. Se usar o Cloud Shell, vá para a etapa 2. Abra uma sessão de comando e entre no Azure com `Connect-AzAccount`.
2. Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos na região Leste do Azure dos EUA:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. Crie uma máquina virtual com o comando [New-AzVM](/powershell/module/az.Compute/New-azVM) . O `-AllocationMethod "Static"` opção atribui um endereço IP público estático para a máquina virtual. O exemplo a seguir cria uma máquina virtual do Windows Server com um endereço IP público, estático e básico da SKU denominado *myPublicIpAddress*. Quando solicitado, forneça um nome de usuário e uma senha para serem usados como as credenciais de login da máquina virtual:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Se o endereço IP público precisar ser um SKU padrão, você deverá [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address), [criar uma interface de rede](virtual-network-network-interface.md#create-a-network-interface), [atribuir o endereço IP público à interface de rede](virtual-network-network-interface-addresses.md#add-ip-addresses) e, em seguida, [crie uma máquina virtual com a interface de rede](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm), em etapas separadas. Saiba mais sobre [SKUs de endereço IP público](./public-ip-addresses.md#sku). Se a máquina virtual for adicionada ao pool de back-end de um Azure Load Balancer público, o SKU do endereço IP público da máquina virtual deverá corresponder ao SKU do endereço IP público do balanceador de carga. Para obter detalhes, consulte [balanceador de carga do Azure](../load-balancer/skus.md).

4. Exiba o endereço IP público atribuído e confirme se ele foi criado como um endereço estático, com [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   O Azure atribuiu um endereço IP público dos endereços usados na região na qual você criou a máquina virtual. Você pode baixar a lista de intervalos (prefixos) para as nuvens [pública](https://www.microsoft.com/download/details.aspx?id=56519), do [governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063), da [China](https://www.microsoft.com/download/details.aspx?id=57062) e da [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

> [!WARNING]
> Não modifique as configurações do endereço IP no sistema operacional da máquina virtual. O sistema operacional fica ciente dos endereços IP públicos do Azure. Embora você possa adicionar configurações de endereço IP privado ao sistema operacional, recomendamos não fazê-lo, a menos que seja necessário, e somente depois de ler [Adicionar um endereço IP privado a um sistema operacional](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Saiba mais sobre [endereços IP privados](./private-ip-addresses.md) e atribuir uma [endereço IP privado estático](virtual-network-network-interface-addresses.md#add-ip-addresses) para uma máquina virtual do Azure
- Saiba mais sobre como criar [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) máquinas virtuais

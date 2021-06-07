---
title: Associar um endereço IP público a uma máquina virtual
titlesuffix: Azure Virtual Network
description: Saiba como associar um endereço IP público a uma VM (máquina virtual) usando o portal do Azure ou o CLI do Azure.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 6ea16da3844b8098d87d65e1016f92c69ae34067
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945163"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associar um endereço IP público a uma máquina virtual

Neste artigo, você aprenderá a associar um endereço IP público a uma VM (máquina virtual) existente. Se você quiser se conectar a uma VM da Internet, a VM deverá ter um endereço IP público associado a ela. Se você quiser criar uma nova VM com um endereço IP público, poderá fazer isso usando o [portal do Azure](virtual-network-deploy-static-pip-arm-portal.md), a [CLI (interface de linha de comando) do Azure](virtual-network-deploy-static-pip-arm-cli.md)ou o [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Endereços IP públicos têm um valor nominal. Para detalhes, consulte o [preço](https://azure.microsoft.com/pricing/details/ip-addresses/). Há um limite para o número de endereços IP públicos que você pode usar por assinatura. Para obter detalhes, consulte [limites](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Você pode usar o [portal do Azure](#azure-portal), a CLI ( [interface de linha de comando](#azure-cli) ) do Azure ou o [PowerShell](#powershell) para associar um endereço IP público a uma VM.

## <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até ou procure a máquina virtual à qual você deseja adicionar o endereço IP público e, em seguida, selecione-a.
3. Em **configurações**, selecione **rede** e, em seguida, selecione o adaptador de rede ao qual você deseja adicionar o endereço IP público, conforme mostrado na figura a seguir:

   ![Selecionar interface de rede](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Os endereços IP públicos são associados às interfaces de rede anexadas a uma VM. Na imagem anterior, a VM tem apenas uma interface de rede. Se a VM tiver várias interfaces de rede, todas elas aparecerão e você selecionaria a interface de rede à qual deseja associar o endereço IP público.

4. Selecione **configurações de IP** e, em seguida, selecione uma configuração de IP, conforme mostrado na figura a seguir:

   ![Selecionar configuração de IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Os endereços IP públicos são associados às configurações de IP para uma interface de rede. Na imagem anterior, a interface de rede tem uma configuração de IP. Se o adaptador de rede tiver várias configurações de IP, elas serão exibidas na lista e você selecionaria a configuração de IP à qual deseja associar o endereço IP público.

5. Selecione **habilitado** e, em seguida, selecione **endereço IP (*definir configurações necessárias*)**. Escolha um endereço IP público existente, que fecha automaticamente a caixa **escolher endereço IP público** . Se você não tiver endereços IP públicos disponíveis listados, será necessário criar um. Para saber como, consulte [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). Selecione **salvar**, conforme mostrado na imagem a seguir e feche a caixa da configuração de IP.

   ![Habilitar endereço IP público](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Os endereços IP públicos que aparecem são aqueles que existem na mesma região que a VM. Se você tiver vários endereços IP públicos criados na região, todos serão exibidos aqui. Se qualquer um estiver esmaecido, é porque o endereço já está associado a um recurso diferente.

6. Exiba o endereço IP público atribuído à configuração de IP, conforme mostrado na imagem a seguir. Pode levar alguns segundos para que um endereço IP apareça.

   ![Exibir endereço IP público atribuído](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > O endereço é atribuído de um pool de endereços usados em cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [Microsoft Azure intervalos de IP do datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nos pools usados para a região. Se você precisar que o endereço seja atribuído de um pool específico na região, use um [prefixo de endereço IP público](public-ip-address-prefix.md).

7. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança em um grupo de segurança de rede.

## <a name="azure-cli"></a>CLI do Azure

Instale a [CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) ou use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Selecione o botão **Experimentar** nos comandos da CLI a seguir. Selecionar **Experimentar** invoca um Cloud Shell com o qual você pode entrar em sua conta do Azure.

1. Se estiver usando a CLI localmente no Bash, entre no Azure com `az login`.
2. Um endereço IP público é associado à configuração de IP de um adaptador de rede anexado a uma VM. Use o comando [AZ Network NIC-IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) para associar um endereço IP público a uma configuração de IP. O exemplo a seguir associa um endereço IP público existente chamado *myVMPublicIP* à configuração de IP chamada *ipconfigmyVM* de uma interface de rede existente chamada *myVMVMNic* que existe em um grupo de recursos chamado *MyResource* Group.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Se você não tiver um endereço IP público existente, use o comando [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) para criar um. Por exemplo, o comando a seguir cria um endereço IP público chamado *myVMPublicIP* em um grupo de recursos chamado *MyResource* Group.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > O comando anterior cria um endereço IP público com valores padrão para várias configurações que você pode desejar personalizar. Para saber mais sobre todas as configurações de endereço IP público, confira [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído de um pool de endereços IP públicos usados para cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [Microsoft Azure intervalos de IP do datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se você não souber o nome de um adaptador de rede anexado à sua VM, use o comando [az vm nic list](/cli/azure/vm/nic#az-vm-nic-list) para exibi-las. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A saída inclui uma ou mais linhas semelhantes ao seguinte exemplo:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome do adaptador de rede.

   - Se não souber o nome de uma configuração de IP para um adaptador de rede, use o comando [az network nic ip-config list](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações de IP para um adaptador de rede chamado *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Exiba o endereço IP público atribuído à configuração de IP com o comando [AZ VM List-IP-addresses](/cli/azure/vm#az-vm-list-ip-addresses) . O exemplo a seguir mostra os endereços IP atribuídos a uma VM existente denominada *myVM* em um grupo de recursos chamado *MyResource* Group.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > O endereço é atribuído de um pool de endereços usados em cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [Microsoft Azure intervalos de IP do datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nos pools usados para a região. Se você precisar que o endereço seja atribuído de um pool específico na região, use um [prefixo de endereço IP público](public-ip-address-prefix.md).

4. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança em um grupo de segurança de rede.

## <a name="powershell"></a>PowerShell

Instale o [PowerShell](/powershell/azure/install-az-ps) ou use o Azure Cloud Shell. O Azure Cloud Shell é um shell gratuito que pode ser executado diretamente no portal do Azure. Ele tem o PowerShell pré-instalado e configurado para usar com sua conta. Selecione o botão **Experimentar** nos comandos do PowerShell a seguir. Selecionar **Experimentar** invoca um Cloud Shell com o qual você pode entrar em sua conta do Azure.

1. Se estiver usando o PowerShell localmente, entre no Azure com `Connect-AzAccount`.
2. Um endereço IP público é associado à configuração de IP de um adaptador de rede anexado a uma VM. Use os comandos [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) e [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) para obter a rede virtual e a sub-rede em que a interface de rede está. Em seguida, use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para obter uma interface de rede e o comando [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para obter um endereço IP público existente. Em seguida, use o comando [set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) para associar o endereço IP público à configuração de IP e o comando [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) para gravar a nova configuração de IP na interface de rede.

   O exemplo a seguir associa um endereço IP público existente chamado *myVMPublicIP* à configuração de IP *chamada ipconfigmyVM* de uma interface de rede existente chamada *myVMVMNic* que existe em uma sub-rede denominada *myVMSubnet* em uma rede virtual chamada *myVMVNet*. Todos os recursos estão em um grupo de recursos chamado *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Se você não tiver um endereço IP público existente, use o comando [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) para criar um. Por exemplo, o comando a seguir cria um endereço IP público *dinâmico* chamado *myVMPublicIP* em um grupo de recursos chamado *MyResource* Group na região *eastus* .
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > O comando anterior cria um endereço IP público com valores padrão para várias configurações que você pode desejar personalizar. Para saber mais sobre todas as configurações de endereço IP público, confira [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído de um pool de endereços IP públicos usados para cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [Microsoft Azure intervalos de IP do datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se você não souber o nome de um adaptador de rede anexado à sua VM, use o comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) para exibi-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída do exemplo, *myVMVMNic* é o nome do adaptador de rede.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se você não souber o nome da rede virtual ou sub-rede em que a interface de rede está, use o `Get-AzNetworkInterface` comando para exibir as informações. Por exemplo, o comando a seguir obtém as informações de rede virtual e de sub-rede para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *MyResource* Group:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMVNET* é o nome da rede virtual e *myVMSubnet* é o nome da sub-rede.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Se não souber o nome de uma configuração de IP para um adaptador de rede, use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações de IP para um adaptador de rede chamado *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *ipconfigmyVM* é o nome de uma configuração de IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Exiba o endereço IP público atribuído à configuração de IP com o comando [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) . O exemplo a seguir mostra o endereço atribuído a um endereço IP público denominado *myVMPublicIP* em um grupo de recursos chamado *MyResource* Group.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Se você não souber o nome do endereço IP público atribuído a uma configuração de IP, execute os seguintes comandos para obtê-lo:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMPublicIP* é o nome do endereço IP público atribuído à configuração de IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > O endereço é atribuído de um pool de endereços usados em cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [Microsoft Azure intervalos de IP do datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nos pools usados para a região. Se você precisar que o endereço seja atribuído de um pool específico na região, use um [prefixo de endereço IP público](public-ip-address-prefix.md).

4. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança em um grupo de segurança de rede.

## <a name="allow-network-traffic-to-the-vm"></a>Permitir o tráfego de rede para a VM

Para poder se conectar ao endereço IP público da Internet, verifique se você tem as portas necessárias abertas em qualquer grupo de segurança de rede associado ao adaptador de rede, se há uma sub-rede na qual o adaptador de rede está ou ambos. Embora os grupos de segurança filtrem o tráfego para o endereço IP privado da interface de rede, quando o tráfego de entrada da Internet chega ao endereço IP público, o Azure converte o endereço público para o endereço IP privado, portanto, se um grupo de segurança de rede impedir o fluxo de tráfego, a comunicação com o endereço IP público falhará. Você pode ver as regras de segurança ativas de uma interface de rede e sua sub-rede usando [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) ou [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Próximas etapas

Permitir tráfego de Internet de entrada para sua VM com um grupo de segurança de rede. Para saber como criar um grupo de segurança de rede, consulte [trabalhar com grupos de segurança de rede](manage-network-security-group.md#work-with-network-security-groups). Para saber mais sobre grupos de segurança de rede, consulte [grupos de segurança](./network-security-groups-overview.md).

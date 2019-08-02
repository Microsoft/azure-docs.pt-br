---
title: Associar um endereço IP público a uma máquina virtual
titlesuffix: Azure Virtual Network
description: Saiba como associar um endereço IP público a uma máquina virtual.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 1b201957a33acd609eed8a2373c8201bdefe9d7d
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64692002"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associar um endereço IP público a uma máquina virtual

Neste artigo, você aprenderá como associar um endereço IP público a uma máquina virtual existente (VM). Se você quiser se conectar a uma VM da internet, a VM deve ter um endereço IP público associado a ele. Se você quiser criar uma nova VM com um endereço IP público, você pode fazer isso usando o [portal do Azure](virtual-network-deploy-static-pip-arm-portal.md), o [interface de linha de comando (CLI) do Azure](virtual-network-deploy-static-pip-arm-cli.md), ou [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Endereços IP públicos têm um valor nominal. Para detalhes, consulte o [preço](https://azure.microsoft.com/pricing/details/ip-addresses/). Há um limite para o número de endereços IP públicos que você pode usar por assinatura. Para obter detalhes, consulte [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Você pode usar o [portal do Azure](#azure-portal), o Azure [interface de linha de comando](#azure-cli) (CLI), ou [PowerShell](#powershell) para associar um endereço IP público a uma VM.

## <a name="azure-portal"></a>Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Procurar ou pesquisar para a máquina virtual que você deseja adicionar o endereço IP público e, em seguida, selecioná-lo.
3. Sob **as configurações**, selecione **Networking**e, em seguida, selecione o adaptador de rede que você deseja adicionar o endereço IP público, conforme mostrado na imagem a seguir:

   ![Selecione o adaptador de rede](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Endereços IP públicos associados a adaptadores de rede anexados a uma VM. Na imagem anterior, a VM tem uma interface de rede. Se a VM tiver várias interfaces de rede, eles seriam exibidos e você selecionaria o adaptador de rede que você deseja associar o endereço IP público.

4. Selecione **configurações de IP** e, em seguida, selecione uma configuração de IP, conforme mostrado na imagem a seguir:

   ![Selecione a configuração de IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Endereços IP públicos são associados a configurações de IP para um adaptador de rede. Na figura anterior, a interface de rede tem uma configuração de IP. Se o adaptador de rede teve várias configurações de IP, eles seriam exibidos na lista e você selecionaria a configuração de IP que você deseja associar o endereço IP público.

5. Selecione **Enabled**, em seguida, selecione **endereço IP (*definir as configurações necessárias*)** . Escolha um endereço IP público existente, que fecha automaticamente o **escolher endereço IP público** caixa. Se você não tiver quaisquer endereços IP públicos disponíveis listados, você precisa criá-lo. Para saber como, consulte [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). Selecione **salvar**, conforme mostrado na imagem que segue e, em seguida, feche a caixa para a configuração de IP.

   ![Habilitar o endereço IP público](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Os endereços IP públicos que aparecem são aqueles que existem na mesma região que a VM. Se você tiver vários endereços IP públicos criados na região, todos serão exibidos aqui. Se qualquer um estiver esmaecido, é porque o endereço já está associado a um recurso diferente.

6. Exiba o endereço IP público atribuído à configuração de IP, conforme mostrado na figura a seguir. Pode levar alguns segundos para um endereço IP seja exibido.

   ![Modo de exibição atribuído o endereço IP público](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > O endereço é atribuído de um pool de endereços usados em cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nos pools de usado para a região. Se você precisa do endereço a ser atribuído de um pool específico na região, use uma [prefixo de endereço IP público](public-ip-address-prefix.md).

7. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança em um grupo de segurança de rede.

## <a name="azure-cli"></a>CLI do Azure

Instalar o [CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), ou usar o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Selecione o **Experimente** botão na CLI comandos a seguir. Selecionando **Experimente** invoca Cloud Shell que você pode entrar sua conta do Azure com o.

1. Se usando a CLI localmente, no Bash, entre no Azure com `az login`.
2. Um endereço IP público é associado a uma configuração de IP de uma interface de rede anexada a uma VM. Use o [atualização de configuração de ip de nic de rede az](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) comando para associar um endereço IP público a uma configuração de IP. O exemplo a seguir associa um endereço IP público existente chamado *myVMPublicIP* para a configuração de IP denominada *ipconfigmyVM* de um adaptador de rede denominado *myVMVMNic* que existe em um grupo de recursos denominado *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Se você não tiver um endereço IP público existente, use o [az network public-ip criar](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) comando para criar um. Por exemplo, o comando a seguir cria um endereço IP público denominado *myVMPublicIP* em um grupo de recursos denominado *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > O comando anterior cria um endereço IP público com valores padrão para várias configurações que talvez você queira personalizar. Para saber mais sobre todas as configurações de endereço IP público, consulte [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído de um pool de endereços IP públicos usados para cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se você não souber o nome de uma interface de rede anexado à sua VM, use o [lista de nic de vm az](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) comando para exibi-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexados a uma VM denominada *myVM* em um grupo de recursos denominado *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome da interface de rede.

   - Se você não souber o nome de uma configuração de IP para um adaptador de rede, use o [lista do az network nic ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) comando recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações de IP de um adaptador de rede denominado *myVMVMNic* em um grupo de recursos denominado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Exibir o endereço IP público atribuído à configuração de IP com o [az vm list-ip-adresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) comando. O exemplo a seguir mostra os endereços IP atribuídos a uma VM existente denominada *myVM* em um grupo de recursos denominado *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > O endereço é atribuído de um pool de endereços usados em cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nos pools de usado para a região. Se você precisa do endereço a ser atribuído de um pool específico na região, use uma [prefixo de endereço IP público](public-ip-address-prefix.md).

4. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança em um grupo de segurança de rede.

## <a name="powershell"></a>PowerShell

Instale [PowerShell](/powershell/azure/install-az-ps), ou usar o Azure Cloud Shell. O Azure Cloud Shell é um shell gratuito que pode ser executado diretamente no portal do Azure. Ele tem o PowerShell pré-instalado e configurado para usar com sua conta. Selecione o **Experimente** botão no PowerShell comandos a seguir. Selecionando **Experimente** invoca Cloud Shell que você pode entrar sua conta do Azure com o.

1. Se estiver usando o PowerShell localmente, entrar no Azure com `Connect-AzAccount`.
2. Um endereço IP público é associado a uma configuração de IP de uma interface de rede anexada a uma VM. Use o [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) e [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) comandos para obter a rede virtual e sub-rede onde está o adaptador de rede. Em seguida, use o [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) comando para obter uma interface de rede e o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) comando para obter um endereço IP público existente. Em seguida, usar o [AzNetworkInterfaceIpConfig conjunto](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) comando para associar o endereço IP público à configuração de IP e o [AzNetworkInterface conjunto](/powershell/module/Az.Network/Set-AzNetworkInterface) comando para gravar a nova configuração de IP para o interface de rede.

   O exemplo a seguir associa um endereço IP público existente chamado *myVMPublicIP* para a configuração de IP denominada *ipconfigmyVM* de um adaptador de rede denominado *myVMVMNic* que existe em uma sub-rede denominada *myVMSubnet* em uma rede virtual denominada *myVMVNet*. Todos os recursos estão em um grupo de recursos denominado *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Se você não tiver um endereço IP público existente, use o [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) comando para criar um. Por exemplo, o comando a seguir cria uma *dinâmica* endereço IP público denominado *myVMPublicIP* em um grupo de recursos denominado *myResourceGroup* no  *eastus* região.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > O comando anterior cria um endereço IP público com valores padrão para várias configurações que talvez você queira personalizar. Para saber mais sobre todas as configurações de endereço IP público, consulte [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído de um pool de endereços IP públicos usados para cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se você não souber o nome de uma interface de rede anexado à sua VM, use o [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) comando para exibi-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexados a uma VM denominada *myVM* em um grupo de recursos denominado *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. A saída de exemplo *myVMVMNic* é o nome da interface de rede.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se você não souber o nome da rede virtual ou sub-rede onde está o adaptador de rede, use o `Get-AzNetworkInterface` comando para exibir as informações. Por exemplo, o comando a seguir obtém as informações de rede e sub-rede virtuais de um adaptador de rede denominado *myVMVMNic* em um grupo de recursos denominado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. A saída de exemplo *myVMVNET* é o nome da rede virtual e *myVMSubnet* é o nome da sub-rede.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Se você não souber o nome de uma configuração de IP para um adaptador de rede, use o [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) comando recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações de IP de um adaptador de rede denominado *myVMVMNic* em um grupo de recursos denominado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. A saída de exemplo *ipconfigmyVM* é o nome de uma configuração de IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Exibir o endereço IP público atribuído à configuração de IP com o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) comando. O exemplo a seguir mostra o endereço atribuído a um endereço IP público denominado *myVMPublicIP* em um grupo de recursos denominado *myResourceGroup*.

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

   A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. A saída de exemplo *myVMPublicIP* é o nome do endereço IP público atribuído à configuração de IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > O endereço é atribuído de um pool de endereços usados em cada região do Azure. Para ver uma lista de pools de endereços usados em cada região, consulte [intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nos pools de usado para a região. Se você precisa do endereço a ser atribuído de um pool específico na região, use uma [prefixo de endereço IP público](public-ip-address-prefix.md).

4. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança em um grupo de segurança de rede.

## <a name="allow-network-traffic-to-the-vm"></a>Permitir o tráfego de rede para a VM

Antes de você pode se conectar ao endereço IP público da internet, certifique-se de que possui as portas necessárias abertos em qualquer grupo de segurança de rede associado ao adaptador de rede, a sub-rede que onde está o adaptador de rede ou ambos. Embora os grupos segurança filtrar o tráfego para o endereço IP do adaptador de rede, depois que o tráfego da internet chega o endereço IP público, o Azure converte o endereço público para o IP privado de endereço, portanto, se um grupo de segurança de rede impede que o Falha do fluxo de tráfego, a comunicação com o endereço IP público. Você pode exibir as regras de segurança em vigor para um adaptador de rede e sua sub-rede usando o [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), ou [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Próximas etapas

Permitir o tráfego da internet à sua VM com um grupo de segurança de rede. Para saber como criar um grupo de segurança de rede, consulte [trabalhar com grupos de segurança de rede](manage-network-security-group.md#work-with-network-security-groups). Para saber mais sobre grupos de segurança de rede, consulte [grupos de segurança](security-overview.md).

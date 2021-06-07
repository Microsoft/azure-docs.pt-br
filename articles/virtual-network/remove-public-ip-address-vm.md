---
title: Desassociar um endereço IP público de uma VM do Azure
titlesuffix: Azure Virtual Network
description: Saiba como desassociar um endereço IP público de uma VM
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: e9bfadd3e2453f0241dc2f7b8bfa5c964333bcf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776532"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Desassociar um endereço IP público de uma VM do Azure 

Neste artigo, você aprende a desassociar um endereço IP público de uma VM (máquina virtual) do Azure.

Você pode usar o [portal do Azure](#azure-portal), a [CLI](#azure-cli) (interface de linha de comando) do Azure ou o [PowerShell](#powershell) para desassociar um endereço IP público de uma VM.

## <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até a máquina virtual da qual deseja desassociar o endereço IP público ou pesquise por ela e, em seguida, selecione-a.
3. Na página da VM, selecione **Visão geral** e selecione o endereço IP público, conforme mostrado na figura a seguir:

   ![Selecionar IP público](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na página do endereço IP público, selecione **Visão geral** e escolha **Desassociar**, conforme mostrado na figura a seguir:

    ![Desassociar IP público](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. Em **Desassociar endereço IP público**, selecione **Sim**.

## <a name="azure-cli"></a>CLI do Azure

Instale a [CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) ou use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Selecione o botão **Experimentar** nos comandos da CLI a seguir. Selecionar **Experimentar** invoca um Cloud Shell com o qual você pode entrar em sua conta do Azure.

1. Se estiver usando a CLI localmente no Bash, entre no Azure com `az login`.
2. Um endereço IP público é associado à configuração de IP de um adaptador de rede anexado a uma VM. Use o comando [az network nic-ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) para desassociar um endereço IP público de uma configuração de IP. O exemplo a seguir desassocia o endereço IP público *myVMPublicIP* da configuração de IP *ipconfigmyVM* de um adaptador de rede existente chamado *myVMVMNic*, anexada a uma VM chamada *myVM* em um grupo de recursos chamado *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Se você não souber o nome de um adaptador de rede anexado à sua VM, use o comando [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) para exibi-las. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A saída inclui uma ou mais linhas semelhantes ao seguinte exemplo:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome do adaptador de rede.

   - Se não souber o nome de uma configuração de IP para um adaptador de rede, use o comando [az network nic ip-config list](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações de IP público para um adaptador de rede chamado *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Se não souber o nome de uma configuração de IP público para um adaptador de rede, use o comando [az network nic ip-config show](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_show) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações de IP público para um adaptador de rede chamado *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Instale o [PowerShell](/powershell/azure/install-az-ps) ou use o Azure Cloud Shell. O Azure Cloud Shell é um shell gratuito que pode ser executado diretamente no portal do Azure. Ele tem o PowerShell pré-instalado e configurado para usar com sua conta. Selecione o botão **Experimentar** nos comandos do PowerShell a seguir. Selecionar **Experimentar** invoca um Cloud Shell com o qual você pode entrar em sua conta do Azure.

1. Se estiver usando o PowerShell localmente, entre no Azure com `Connect-AzAccount`.
2. Um endereço IP público é associado à configuração de IP de um adaptador de rede anexado a uma VM. Use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para obter um adaptador de rede. Defina o valor do endereço IP público como nulo e use o comando [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) para gravar a nova configuração de IP no adaptador de rede.

   O exemplo a seguir desassocia um endereço IP público chamado *myVMPublicIP* de um adaptador de rede chamado *myVMVMNic* que está anexado a uma VM chamada *myVM*. Todos os recursos estão em um grupo de recursos chamado *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Se você não souber o nome de um adaptador de rede anexado à sua VM, use o comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) para exibi-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída do exemplo, *myVMVMNic* é o nome do adaptador de rede.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se não souber o nome de uma configuração de IP para um adaptador de rede, use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações de IP para um adaptador de rede chamado *myVMVMNic* em um grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo a seguir. Na saída de exemplo, *ipconfigmyVM* é o nome de uma configuração de IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Próximas etapas

- Saiba como [associar um endereço IP público a uma VM](associate-public-ip-address-vm.md).

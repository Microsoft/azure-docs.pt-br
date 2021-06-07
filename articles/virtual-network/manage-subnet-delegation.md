---
title: Adicionar ou remover uma delegação de sub-rede em uma rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como adicionar ou remover uma sub-rede delegada para um serviço no Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 2bb80ba421617d5fd1699826deda00e56f1e43af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943662"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Adicionar ou remover uma delegação de sub-rede

A delegação de sub-rede dá permissões explícitas ao serviço para criar recursos específicos do serviço na sub-rede, usando um identificador exclusivo ao implantar o serviço. Este artigo descreve como adicionar ou remover uma sub-rede delegada para um serviço do Azure.

## <a name="portal"></a>Portal

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e a sub-rede que você delegará posteriormente a um serviço do Azure.

1. No lado superior esquerdo da tela, selecione **criar um recurso** rede redes  >    >  **virtuais**.
1. Em **Criar rede virtual**, insira ou selecione estas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork*. |
    | Espaço de endereço | Insira *10.0.0.0/16*. |
    | Subscription | Selecione sua assinatura.|
    | Resource group | Selecione **Criar novo** e insira *myResourceGroup*, depois selecione **OK**. |
    | Location | Selecione **lesteus**.|
    | Sub-rede – Nome | Insira *mysubnet*. |
    | Sub-rede – Intervalo de endereços | Insira *10.0.0.0/24*. |
    |||
1. Deixe o restante como padrão e, em seguida, selecione **criar**.

### <a name="permissions"></a>Permissões

Se você não criou a sub-rede que deseja delegar a um serviço do Azure, precisará da seguinte permissão: `Microsoft.Network/virtualNetworks/subnets/write` .

A função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) interna também contém as permissões necessárias.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegar uma sub-rede a um serviço do Azure

Nesta seção, você delega a sub-rede que criou na seção anterior para um serviço do Azure.

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **sub-redes**, em **configurações** e, em seguida, selecione **mysubnet**.
4. Na página *mysubnet* , para a lista **delegação de sub-rede** , selecione os serviços listados em **delegar sub-rede para um serviço** (por exemplo, **Microsoft. DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Remover a delegação de sub-rede de um serviço do Azure

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **sub-redes**, em **configurações** e, em seguida, selecione **mysubnet**.
4. Na página *mysubnet* , para a lista **delegação de sub-rede** , selecione **nenhum** dos serviços listados em **delegar sub-rede a um serviço**. 

## <a name="azure-cli"></a>CLI do Azure

Prepare seu ambiente para a CLI do Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroup** na localização **eastus**:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** no **myResourceGroup** usando [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Permissões

Se você não criou a sub-rede que deseja delegar a um serviço do Azure, precisará da seguinte permissão: `Microsoft.Network/virtualNetworks/subnets/write` .

A função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) interna também contém as permissões necessárias.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegar uma sub-rede a um serviço do Azure

Nesta seção, você delega a sub-rede que criou na seção anterior para um serviço do Azure. 

Use [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) para atualizar a sub-rede chamada **mysubnet** com uma delegação para um serviço do Azure.  Neste exemplo, **Microsoft. DBforPostgreSQL/serversv2** é usado para a delegação de exemplo:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Para verificar se a delegação foi aplicada, use [AZ Network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show). Verifique se o serviço é delegado à sub-rede sob a propriedade **ServiceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Remover a delegação de sub-rede de um serviço do Azure

Use [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) para remover a delegação da sub-rede chamada **mysubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Para verificar se a delegação foi removida, use [AZ Network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show). Verifique se o serviço foi removido da sub-rede sob a propriedade **ServiceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
A saída do comando é um colchete nulo:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Conectar-se ao Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos com [New-AzResourceGroup](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Criar rede virtual

Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) no **myResourceGroup** usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O espaço de endereço IP para a rede virtual é **10.0.0.0/16**. A sub-rede na rede virtual é **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Permissões

Se você não criou a sub-rede que deseja delegar a um serviço do Azure, precisará da seguinte permissão: `Microsoft.Network/virtualNetworks/subnets/write` .

A função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) interna também contém as permissões necessárias.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegar uma sub-rede a um serviço do Azure

Nesta seção, você delega a sub-rede que criou na seção anterior para um serviço do Azure. 

Use [Add-AzDelegation](/powershell/module/az.network/add-azdelegation) para atualizar a sub-rede chamada **mysubnet** com uma delegação chamada **Mydelegation** para um serviço do Azure.  Neste exemplo, **Microsoft. DBforPostgreSQL/serversv2** é usado para a delegação de exemplo:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Use [Get-AzDelegation](/powershell/module/az.network/get-azdelegation) para verificar a delegação:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Remover a delegação de sub-rede de um serviço do Azure

Use [Remove-AzDelegation](/powershell/module/az.network/remove-azdelegation) para remover a delegação da sub-rede chamada **mysubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Use [Get-AzDelegation](/powershell/module/az.network/get-azdelegation) para verificar se a delegação foi removida:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Próximas etapas
- Saiba como [gerenciar sub-redes no Azure](virtual-network-manage-subnet.md).

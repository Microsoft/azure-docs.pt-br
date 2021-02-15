---
title: Criar uma rede virtual – início rápido – Azure PowerShell
titlesuffix: Azure Virtual Network
description: Neste início rápido, você cria uma rede virtual usando o portal do Azure. A rede virtual permite que os recursos do Azure se comuniquem entre si e com a Internet.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93e459df96d444e71f4b6a15668f80e9d77db5fd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89077867"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Início Rápido: Criar uma rede virtual usando o PowerShell

Uma rede virtual permite que recursos do Azure, como VMs (máquinas virtuais), comuniquem-se em modo privado e com a Internet. Neste início rápido, você aprende como criar uma rede virtual. Após criar uma rede virtual, você implantará duas VMs na rede virtual. Você fará a conexão com as VMs usando a Internet e se comunicará de modo privado na rede virtual.

## <a name="prerequisites"></a>Pré-requisitos
Caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar o PowerShell localmente, este início rápido exigirá que você use o módulo do Azure PowerShell versão 1.0.0 ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps) para instalação e atualização de informações.

Por fim, se você estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount`. Esse comando cria uma conexão com o Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Crie um grupo de recursos e uma rede virtual

Há uma série de etapas que você precisa percorrer para configurar seu grupo de recursos e rede virtual.

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de poder criar uma rede virtual, você deverá criar um grupo de recursos para hospedá-la. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na região *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* na localização *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

O Azure implanta recursos em uma sub-rede dentro de uma rede virtual, portanto, é necessário criar uma sub-rede. Criar uma configuração de sub-rede chamada *padrão* com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associar a sub-rede à rede virtual

Você pode gravar a configuração da sub-rede na rede virtual com [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Este comando cria a sub-rede:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Criar a primeira VM com [New-AzVM](/powershell/module/az.compute/new-azvm). Quando você executa o próximo comando, é solicitado a informar as credenciais. Insira um nome de usuário e senha para a VM:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

A opção `-AsJob` cria a VM em segundo plano. Você pode continuar para a próxima etapa.

Quando o Azure inicia a criação da VM em segundo plano, você receberá de volta algo parecido com isto:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Crie a segunda VM com este comando:

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Você precisará criar outro usuário e senha. O Azure reserva alguns minutos para criar a VM.

> [!IMPORTANT]
> Não continue com a próxima etapa até que o Azure tenha terminado.  Você saberá que está pronto quando ele retornar uma saída ao PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público de uma VM. O exemplo retorna o endereço IP público da VM *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Abra um prompt de comando no computador local. Execute o comando `mstsc`. Substitua `<publicIpAddress>` pelo endereço IP público retornado da última etapa:

> [!NOTE]
> Se você estiver executando esses comandos em um prompt do PowerShell no computador local e estiver usando a versão 1.0 ou posterior do módulo Az PowerShell, poderá continuar nessa interface.

```cmd
mstsc /v:<publicIpAddress>
```
1. Se solicitado, selecione **Conectar**.

1. Insira o nome de usuário e senha que você especificou ao criar a VM.

    > [!NOTE]
    > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você pode receber um aviso de certificado. Se você decidir, selecione **Sim** ou **Continuar**.

## <a name="communicate-between-vms"></a>Comunicação entre VMs

1. Na Área de Trabalho Remota do *myVm1*, abra o PowerShell.

1. Digite `ping myVm2`.

    Você obterá algo parecido com isto de volta:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    O ping falha, pois ele usa o ICMP (Internet Control Message Protocol). Por padrão, o ICMP não é permitido pelo Firewall do Windows.

1. Para permitir que *myVm2* execute o ping *myVm1* em uma etapa posterior, digite este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Esse comando permite a entrada ICMP pelo Firewall do Windows.

1. Feche a conexão da área de trabalho remota para *myVm1*.

1. Repita as etapas em [Conectar-se a uma VM da Internet](#connect-to-a-vm-from-the-internet). Desta vez, conecte-se ao *myVm2*.

1. A partir de um prompt de comando na VM *myVm2*, insira `ping myvm1`.

    Você obterá algo parecido com isto de volta:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Você recebe respostas de *myVm1*, porque você permitiu ICMP através do firewall do Windows na VM *myVm1* em uma etapa anterior.

1. Feche a conexão da área de trabalho remota para *myVm2*.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao terminar a rede virtual e as VMs, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele tem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma rede virtual padrão e duas VMs. Você se conectou a uma VM pela Internet e executou uma comunicação entre duas VMs em modo privado.
O Azure permite comunicação privada irrestrita entre VMs. Por padrão, ele só permite conexões de área de trabalho remota de entrada para VMs do Windows da Internet. Prossiga para o próximo artigo para aprender a configurar diferentes tipos de comunicações de rede de VMs:
> [!div class="nextstepaction"]
> [Filtrar tráfego de rede](tutorial-filter-network-traffic.md)

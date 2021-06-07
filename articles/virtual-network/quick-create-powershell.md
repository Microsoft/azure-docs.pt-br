---
title: Criar uma rede virtual – início rápido – Azure PowerShell
titlesuffix: Azure Virtual Network
description: Neste início rápido, você cria uma rede virtual usando o portal do Azure. A rede virtual permite que os recursos do Azure se comuniquem entre si e com a Internet.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7ee10327ab95a3e66e5592593ae72d6e5cd8d606
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060594"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Início Rápido: Criar uma rede virtual usando o PowerShell

Uma rede virtual permite que recursos do Azure, como VMs (máquinas virtuais), comuniquem-se em modo privado e com a Internet. 

Neste início rápido, você aprende como criar uma rede virtual. Após criar uma rede virtual, você implantará duas VMs na rede virtual. Você fará a conexão com as VMs usando a Internet e se comunicará de modo privado na rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente ou Azure Cloud Shell

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Crie um grupo de recursos e uma rede virtual

Há uma série de etapas que você precisa percorrer para configurar seu grupo de recursos e rede virtual.

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de poder criar uma rede virtual, você deverá criar um grupo de recursos para hospedá-la. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Este exemplo cria um grupo de recursos chamado **CreateVNetQS-rg** na localização **Eastus**:

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Este exemplo cria uma rede virtual padrão chamada **myVNet** na localização **EastUS**:

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

O Azure implanta recursos em uma sub-rede dentro de uma rede virtual, portanto, é necessário criar uma sub-rede. Criar uma configuração de sub-rede chamada **padrão** com [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
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
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
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
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

Você precisará criar outro usuário e senha. O Azure reserva alguns minutos para criar a VM.

> [!IMPORTANT]
> Não continue com a próxima etapa até que o Azure tenha terminado.  Você saberá que está pronto quando ele retornar uma saída ao PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Para obter o endereço IP público da VM, use o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress).

O exemplo retorna o endereço IP público da VM **myVm1**:

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
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

1. Na Área de Trabalho Remota do **myVm1**, abra o PowerShell.

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

1. Para permitir que **myVm2** execute o ping **myVm1** em uma etapa posterior, digite este comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Esse comando permite a entrada ICMP pelo Firewall do Windows.

1. Feche a conexão da área de trabalho remota para **myVm1**.

1. Repita as etapas em [Conectar-se a uma VM da Internet](#connect-to-a-vm-from-the-internet). Desta vez, conecte-se ao **myVm2**.

1. A partir de um prompt de comando na VM **myVm2**, insira `ping myvm1`.

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

    Você recebe respostas de **myVm1**, porque você permitiu ICMP através do firewall do Windows na VM **myVm1** em uma etapa anterior.

1. Feche a conexão da área de trabalho remota para **myVm2**.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao terminar a rede virtual e as VMs, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele tem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido: 

* Você criou uma rede virtual padrão e duas VMs. 
* Você se conectou a uma VM pela Internet e executou uma comunicação entre duas VMs em modo privado.

A comunicação privada entre as VMs é irrestrita em uma rede virtual. 

Prossiga para o próximo artigo para aprender a configurar diferentes tipos de comunicações de rede de VMs:
> [!div class="nextstepaction"]
> [Filtrar tráfego de rede](tutorial-filter-network-traffic.md)

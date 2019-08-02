---
title: Tutorial – Criar e gerenciar redes virtuais do Azure para VMs Windows | Microsoft Docs
description: Neste tutorial, você aprende a usar Microsoft Azure PowerShell para criar e gerenciar redes virtuais do Azure para máquinas virtuais Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d283a18d0ec2391b05210b785351dda0ca0ad416
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707905"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Tutorial: Criar e gerenciar redes virtuais do Microsoft Azure para as máquinas virtuais do Windows com o Microsoft Azure PowerShell

As máquinas virtuais do Azure usam a rede do Azure para comunicação de rede interna e externa. Este tutorial explica como implantar duas máquinas virtuais e configurar a rede do Azure para essas VMs. Os exemplos neste tutorial supõem que as VMs estão hospedando um aplicativo Web com um back-end de banco de dados. No entanto, não há implantação de aplicativo no tutorial. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar a rede virtual e a sub-rede
> * Criar um endereço IP público
> * Criar uma VM de front-end
> * Protegem o tráfego de rede
> * Criar VM back-end


## <a name="vm-networking-overview"></a>Visão Geral da VM

As redes virtuais do Azure habilitam conexões de rede seguras entre máquinas virtuais, Internet e outros serviços do Azure, como banco de dados SQL do Azure. As redes virtuais são divididas em segmentos lógicos chamados sub-redes. As sub-redes são usadas para controlar o fluxo de rede e como um limite de segurança. Ao implantar uma máquina virtual, ela geralmente inclui um adaptador de rede virtual, que é anexado a uma sub-rede.

Ao concluir este tutorial, você poderá ver estes recursos criados:

![Rede virtual com duas sub-redes](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – a rede virtual que as VMs usam para se comunicar entre si e com a Internet.
- *myFrontendSubnet* – a sub-rede em *myVNet* usada pelos recursos de front-end.
- *myPublicIPAddress* – o endereço IP público usado para acessar *myFrontendVM* da Internet.
- *myFrontendNic* – a interface de rede usada pelo *myFrontendVM* para se comunicar com *myBackendVM*.
- *myFrontendVM* – a VM usada para comunicação entre a Internet e *myBackendVM*.
- *myBackendNSG* – o grupo de segurança de rede que controla a comunicação entre o *myFrontendVM* e *myBackendVM*.
- *myBackendSubnet* – a sub-rede associada a *myBackendNSG* e usada pelos recursos de back-end.
- *myBackendNic* – O adaptador de rede usado pelo *myBackendVM* para se comunicar com *myFrontendVM*.
- *myBackendVM* – A VM que usa a porta 1433 para se comunicar com *myFrontendVM*.


## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.


## <a name="create-subnet"></a>Criar sub-rede 

Para este tutorial, uma única rede virtual é criada com duas sub-redes. Uma sub-rede de front-end para hospedar um aplicativo Web e uma sub-rede de back-end para hospedar um servidor de banco de dados.

Antes de criar uma rede virtual, crie um grupo de recursos usando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). O exemplo abaixo cria um grupo de recursos denominado *myRGNetwork* no local *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Crie uma configuração de sub-rede chamada *myFrontendSubnet* usando [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

E, criar uma configuração de sub-rede chamada *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Criar rede virtual

Crie uma VNET denominada *myVNet* usando *myFrontendSubnet* e *myBackendSubnet* usando [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Neste ponto, uma rede foi criada e segmentada em duas sub-redes, uma para os serviços de front-end e outra para serviços de back-end. Na próxima seção, as máquinas virtuais serão criadas e conectadas a essas sub-redes.

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Um endereço IP público permite que os recursos do Azure sejam acessados pela Internet. O método de alocação do endereço IP público pode ser configurado como dinâmico ou estático. Por padrão, um endereço IP público é alocado dinamicamente. Os endereços IP dinâmicos são liberados quando uma VM é desalocada. Esse comportamento faz com que o endereço IP se altere durante operações que incluam uma desalocação de VM.

O método de alocação pode ser definido como estático, o que garante que o endereço IP permaneça atribuído a uma VM mesmo durante um estado desalocado. Se estiver usando um endereço IP estático, o próprio endereço IP não poderá ser especificado. Em vez disso, ele será alocado de um pool de endereços disponíveis.

Crie um endereço IP público denominado *myPublicIPAddress* usando [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress):

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

Você pode alterar o parâmetro -AllocationMethod para `Static` para atribuir um endereço IP público estático.

## <a name="create-a-front-end-vm"></a>Criar uma VM de front-end

Para uma VM se comunicar em uma rede virtual, ela precisará de uma adaptador de rede virtual (NIC). Crie uma NIC usando [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Defina o nome de usuário e a senha necessários para a conta de administrador na VM usando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential). Você usa essas credenciais para conectar-se à VM nas etapas adicionais:

```azurepowershell-interactive
$cred = Get-Credential
```

Crie as VMs usando [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Protegem o tráfego de rede

Um NSG (grupo de segurança de rede) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos conectados a VNets (redes virtuais) do Azure. Os NSGs podem ser associados a sub-redes ou adaptadores de rede individuais. Um NSG associado a um adaptador de rede se aplica somente à VM associada. Quando um NSG está associado a uma sub-rede, as regras se aplicam a todos os recursos conectados à sub-rede.

### <a name="network-security-group-rules"></a>Regras de grupo de segurança de rede

As regras NSG definem portas de rede pelas quais o tráfego é permitido ou negado. As regras podem incluir intervalos de endereços IP de origem e de destino, para que o tráfego seja controlado entre sistemas ou sub-redes específicos. As regras NSG também incluem uma prioridade (entre 1 e 4096). As regras são avaliadas na ordem de prioridade. Uma regra com uma prioridade 100 é avaliada antes de uma regra com prioridade 200.

Todos os NSGs contêm um conjunto de regras padrão. As regras padrão não podem ser excluídas, mas como recebem a prioridade mais baixa, elas podem ser substituídas pelas regras que você criar.

- **Rede virtual:** o tráfego que começa e termina em uma rede virtual é permitido nas direções de entrada e saída.
- **Internet:** o tráfego de saída é permitido, mas o tráfego de entrada é bloqueado.
- **Balanceador de carga:** o balanceador de carga do Azure permite investigar a integridade de suas VMs e instâncias de função. Se não estiver usando um conjunto de balanceamento de carga, você poderá substituir essa regra.

### <a name="create-network-security-groups"></a>Criar grupos de segurança de rede

Crie uma regra de entrada denominada *myFrontendNSGRule* para permitir o tráfego da Web em *myFrontendVM* usando [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Você pode limitar o tráfego interno para *myBackendVM* apenas de *myFrontendVM* criando um NSG para a sub-rede de back-end. O exemplo a seguir cria uma regra NSG chamada *myBackendNSGRule*:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Adicione um grupo de segurança de rede chamado *myFrontendNSG* usando [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Agora, adicione um grupo de segurança de rede chamado *myBackendNSG* usando New-AzNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Adicione os grupos de segurança de rede às sub-redes.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Criar uma VM de back-end

A maneira mais fácil de criar a VM de back-end para este tutorial é usar uma imagem do SQL Server. Este tutorial apenas cria a VM com o servidor de banco de dados, mas não fornece informações sobre como acessá-lo.

Crie *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Defina o nome de usuário e a senha necessários para a conta de administrador na VM com Get-Credential:

```azurepowershell-interactive
$cred = Get-Credential
```

Crie *myBackendVM*.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

A imagem neste exemplo tem o SQL Server instalado, mas não é usada neste tutorial. Ela está incluída para mostrar como é possível configurar uma VM para lidar com o tráfego da Web e uma VM para lidar com o gerenciamento de banco de dados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou e protegeu redes do Azure em relação às máquinas virtuais. 

> [!div class="checklist"]
> * Criar a rede virtual e a sub-rede
> * Criar um endereço IP público
> * Criar uma VM de front-end
> * Protegem o tráfego de rede
> * Criar uma VM de back-end

Avance para o próximo tutorial a fim de aprender sobre como monitorar e proteger dados em máquinas virtuais usando o backup do Azure.

> [!div class="nextstepaction"]
> [Fazer backup de máquinas virtuais do Windows no Azure](./tutorial-backup-vms.md)

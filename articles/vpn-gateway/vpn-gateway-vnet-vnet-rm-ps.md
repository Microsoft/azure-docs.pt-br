---
title: 'Conectar uma rede virtual do Azure a outra rede virtual usando uma conexão de VNet a VNet: PowerShell | Microsoft Docs'
description: Conecte redes virtuais usando a conexão de rede virtual a rede virtual e o PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: cherylmc
ms.openlocfilehash: dbf59740af64bf8d403b6596a17646304c0f1eb0
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385782"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Configurar uma conexão gateway de VPN de Vnet pra VNet usando o PowerShell

Este artigo ajuda você a conectar redes virtuais usando o tipo de conexão de rede virtual a rede virtual. As redes virtuais podem estar na mesma região ou em regiões diferentes, e com a mesma assinatura ou em assinaturas diferentes. Ao conectar-se a redes virtuais a partir de assinaturas diferentes, as assinaturas não precisam ser associadas ao mesmo locatário do Active Directory.

As etapas neste artigo se aplicam ao modelo de implantação do Gerenciador de Recursos e usa o PowerShell. Você também pode criar essa configuração usando uma ferramenta de implantação ou um modelo de implantação diferente, selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conectar modelos de implantação diferentes – portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conectar modelos de implantação diferentes - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

## <a name="about"></a>Sobre a conexão de VNets

Há várias maneiras de conectar redes virtuais. As seções a seguir descrevem as diferentes maneiras de conectar redes virtuais.

### <a name="vnet-to-vnet"></a>Redes Virtuais a Redes Virtuais

A configuração de uma conexão VNet a VNet é uma boa maneira de conectar redes virtuais com facilidade. A conexão de uma rede virtual a outra rede virtual usando o tipo de conexão VNet a VNet (VNet2VNet) é semelhante à criação de uma conexão IPsec Site a Site com um site local.  Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE, e ambos funcionam da mesma forma ao se comunicar. A diferença entre os tipos de conexão é a maneira que o gateway de rede local é configurado. Ao criar uma conexão de VNet a VNet, você não vê o espaço de endereço de gateway de rede local. Ele é criado e preenchido automaticamente. Se você atualizar o espaço de endereço de uma VNet, a outra saberá automaticamente como rotear para o espaço de endereço atualizado. A criação de uma conexão VNet a VNet é normalmente mais rápida e fácil do que a criação de uma conexão Site a Site entre VNets.

### <a name="site-to-site-ipsec"></a>Site a Site (IPsec)

Se você estiver trabalhando com uma configuração de rede complicada, talvez prefira se conectar suas redes virtuais usando as etapas de [Site a Site](vpn-gateway-create-site-to-site-rm-powershell.md) etapas, em vez das etapas de VNet para VNet. Quando você usar as etapas de Site a Site, vai criar e configurar os gateways de rede local manualmente. O gateway de rede local para cada VNet trata a outra VNet como um site local. Isso permite que você especifique o espaço de endereço adicional para o gateway de rede local a fim de rotear o tráfego. Se o espaço de endereço para uma rede virtual for alterado, você precisará atualizar o gateway de rede local correspondente para refletir a alteração. Ele não é atualizado automaticamente.

### <a name="vnet-peering"></a>Emparelhamento VNet

Talvez você queira considerar a conexão de suas VNets usando o Emparelhamento de VNet. O emparelhamento de VNet não usa um gateway de VPN e tem restrições diferentes. Além disso, os [preços do emparelhamento VNet](https://azure.microsoft.com/pricing/details/virtual-network) são calculados de maneira diferente dos [preços de Gateway de VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Por que criar uma conexão de VNet para VNet?

Talvez você queira conectar redes virtuais usando uma conexão VNet a VNet pelos seguintes motivos:

* **Redundância geográfica entre regiões e presença geográfica**

  * Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade voltados para a Internet.
  * Com o Balanceador de Carga e o Gerenciador de Tráfego do Azure você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Um exemplo importante é configurar o Always On do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.
* **Aplicativos multicamadas regionais com limite administrativo ou de isolamento**

  * Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas devido aos requisitos administrativos ou de isolamento.

Você pode combinar a comunicação de VNet a VNet usando configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.

## <a name="steps"></a>Quais etapas de VNet a VNet devo usar?

Neste artigo, você verá dois conjuntos de etapas diferentes. Um conjunto de etapas para [VNets que residem na mesma assinatura](#samesub), e um para [VNets que residem em assinaturas diferentes](#difsub).
A principal diferença entre os conjuntos é que você deve usar sessões separadas do PowerShell ao configurar as conexões para VNets que residem em assinaturas diferentes. 

Para este exercício, você pode combinar as configurações, ou simplesmente escolher aquela com a qual você deseja trabalhar. Todas as configurações usam o tipo de conexão VNet a VNet. O tráfego de rede flui entre as VNets diretamente conectadas umas às outras. Neste exercício, o tráfego de TestVNet4 não roteia para TestVNet5.

* [VNets que estão na mesma assinatura](#samesub): As etapas para essa configuração usam TestVNet1 e TestVNet4.

  ![Diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [VNets que estão em assinaturas diferentes](#difsub): As etapas para essa configuração usam TestVNet1 e TestVNet5.

  ![Diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="samesub"></a>Como conectar VNets que estão na mesma assinatura

### <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Como a criação de um gateway leva até 45 minutos, o Azure Cloud Shell atingirá o tempo limite periodicamente durante o exercício. Você pode reiniciar o Cloud Shell clicando no canto superior esquerdo do terminal. Redeclare todas as variáveis quando reiniciar o terminal.

* Se você preferir instalar a versão mais recente do módulo do Azure PowerShell localmente, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="Step1"></a>Etapa 1 – Planejar os intervalos de endereços IP

Nas etapas a seguir, crie duas redes virtuais juntamente com as respectivas configurações e sub-redes de gateway. Em seguida, crie uma conexão VPN entre as duas VNets. É importante planejar os intervalos de endereços IP para sua configuração de rede. Lembre-se de que você deve garantir que nenhum de seus intervalos de VNet ou intervalos de rede local se sobreponham de forma alguma. Nestes exemplos, não incluímos um servidor DNS. Se você deseja resolução de nomes para suas redes virtuais, confira [a Resolução de nomes](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Usamos os seguintes valores nos exemplos:

**Valores para TestVNet1:**

* Nome da VNet: TestVNet1
* Grupo de Recursos: TestRG1
* Local: East US
* TestVNet1: 10.11.0.0/16 e 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* IP público: VNet1GWIP
* VPNType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5 (para VNets em assinaturas diferentes)
* ConnectionType: VNet2VNet

**Valores para TestVNet4:**

* Nome da VNet: TestVNet4
* TestVNet2: 10.41.0.0/16 e 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Grupo de Recursos: TestRG4
* Local: Oeste dos EUA
* GatewayName: VNet4GW
* IP público: VNet4GWIP
* VPNType: RouteBased
* Conexão: VNet4toVNet1
* ConnectionType: VNet2VNet


### <a name="Step2"></a>Etapa 2: Criar e configurar o TestVNet1

1. Verifique as configurações de assinatura.

   Conecte-se à sua conta se você estiver executando o PowerShell localmente no seu computador. Se você estiver usando o Azure Cloud Shell, será conectado automaticamente.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Verificar as assinaturas da conta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Se você tiver mais de uma assinatura, especifique a assinatura que deseja usar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName nameofsubscription
   ```
2. Declare as variáveis. Este exemplo declara as variáveis usando os valores para este exercício. Na maioria dos casos, substitua os valores pelos seus próprios. No entanto, se você estiver executando as etapas para se familiarizar com esse tipo de configuração, poderá usar essas variáveis. Modifique as variáveis, se for necessário, e depois copie e cole em seu console do PowerShell.

   ```azurepowershell-interactive
   $RG1 = "TestRG1"
   $Location1 = "East US"
   $VNetName1 = "TestVNet1"
   $FESubName1 = "FrontEnd"
   $BESubName1 = "Backend"
   $VNetPrefix11 = "10.11.0.0/16"
   $VNetPrefix12 = "10.12.0.0/16"
   $FESubPrefix1 = "10.11.0.0/24"
   $BESubPrefix1 = "10.12.0.0/24"
   $GWSubPrefix1 = "10.12.255.0/27"
   $GWName1 = "VNet1GW"
   $GWIPName1 = "VNet1GWIP"
   $GWIPconfName1 = "gwipconf1"
   $Connection14 = "VNet1toVNet4"
   $Connection15 = "VNet1toVNet5"
   ```
3. Crie um grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
4. Crie as configurações de sub-rede para TestVNet1. Este exemplo cria uma rede virtual denominada TestVNet1 e três sub-redes, uma chamada GatewaySubnet, outra FrontEnd e outra Backend. Ao substituir valores, é importante você sempre nomear sua sub-rede de gateway especificamente como GatewaySubnet. Se você usar outro nome, a criação do gateway falhará. Por esse motivo, ele não é atribuído por meio da variável abaixo.

   O exemplo a seguir usa as variáveis que você definiu anteriormente. Neste exemplo, a sub-rede de gateway está usando um /27. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, recomendamos que você crie uma sub-rede maior que inclua mais endereços selecionando pelo menos /28 ou /27. Isso permitirá endereços suficientes a fim de acomodar as possíveis configurações adicionais que você possa querer no futuro.

   ```azurepowershell-interactive
   $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
   $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
   $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix1
   ```
5. Crie a TestVNet1.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
   ```
6. Solicite um endereço IP público para ser alocado ao gateway que você criará para sua VNet. Observe que o AllocationMethod é dinâmico. Você não pode especificar o endereço IP que deseja usar. Ele é alocado dinamicamente ao gateway. 

   ```azurepowershell-interactive
   $gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AllocationMethod Dynamic
   ```
7. Crie a configuração do gateway. A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo para criar a configuração do gateway.

   ```azurepowershell-interactive
   $vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
   $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
   -Subnet $subnet1 -PublicIpAddress $gwpip1
   ```
8. Crie o gateway para TestVNet1. Nesta etapa, você criará o gateway de rede virtual para sua TestVNet1. As configurações de rede virtual com rede virtual exigem um VpnType RouteBased. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
   -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

Depois de concluir os comandos, o gateway levará até 45 minutos para ser criado. Se você estiver usando o Azure Cloud Shell, poderá reiniciar a sessão do CloudShell clicando no canto superior esquerdo do terminal do Cloud Shell; em seguida, configure TestVNet4. Você não precisa esperar até que o gateway TestVNet1 seja concluído.

### <a name="step-3---create-and-configure-testvnet4"></a>Etapa 3: criar e configurar TestVNet4

Depois de configurar TestVNet1, crie TestVNet4. Siga as etapas abaixo, substituindo os valores pelos seus próprios quando necessário.

1. Conecte-se e declare as variáveis. Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração.

   ```azurepowershell-interactive
   $RG4 = "TestRG4"
   $Location4 = "West US"
   $VnetName4 = "TestVNet4"
   $FESubName4 = "FrontEnd"
   $BESubName4 = "Backend"
   $VnetPrefix41 = "10.41.0.0/16"
   $VnetPrefix42 = "10.42.0.0/16"
   $FESubPrefix4 = "10.41.0.0/24"
   $BESubPrefix4 = "10.42.0.0/24"
   $GWSubPrefix4 = "10.42.255.0/27"
   $GWName4 = "VNet4GW"
   $GWIPName4 = "VNet4GWIP"
   $GWIPconfName4 = "gwipconf4"
   $Connection41 = "VNet4toVNet1"
   ```
2. Crie um grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG4 -Location $Location4
   ```
3. Crie as configurações de sub-rede para TestVNet4.

   ```azurepowershell-interactive
   $fesub4 = New-AzVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
   $besub4 = New-AzVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
   $gwsub4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix4
   ```
4. Crie a TestVNet4.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
   ```
5. Solicite um endereço IP público.

   ```azurepowershell-interactive
   $gwpip4 = New-AzPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AllocationMethod Dynamic
   ```
6. Crie a configuração do gateway.

   ```azurepowershell-interactive
   $vnet4 = Get-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
   $subnet4 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
   $gwipconf4 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
   ```
7. Crie o gateway TestVNet4. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
   -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-4---create-the-connections"></a>Etapa 4: criar as conexões

Aguarde até que ambos os gateways sejam concluídos. Reinicie a sessão do Azure Cloud Shell e copie e cole as variáveis do início das Etapas 2 e 3 no console para redeclarar valores.

1. Obter ambos os gateways de rede virtual.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   $vnet4gw = Get-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
   ```
2. Crie a conexão de TestVNet1 a TestVNet4. Nesta etapa, você criará a conexão de TestVNet1 para TestVNet4. Você verá uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. O importante é que a chave compartilhada deve corresponder em ambas as conexões. Criar uma conexão pode levar alguns minutos para ser concluída.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
   -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
3. Crie a conexão de TestVNet4 a TestVNet1. Esta etapa é semelhante à etapa mostrada acima, exceto que você está criando a conexão de TestVNet4 para TestVNet1. Verifique se que as chaves compartilhadas correspondem. Depois de alguns minutos, a conexão deverá ser estabelecida.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
   -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. Verifique a conexão. Consulte a seção [Como verificar sua conexão](#verify).

## <a name="difsub"></a>Como conectar as VNets que estão em assinaturas diferentes

Nesse cenário, conecte TestVNet1 e TestVNet5. TestVNet1 e TestVNet5 residem em assinaturas diferentes. As assinaturas não precisam ser associadas ao mesmo locatário do Active Directory.

A diferença entre essas etapas e o conjunto anterior é que algumas das etapas de configuração precisam ser executadas em uma sessão separada do PowerShell no contexto da segunda assinatura. Especialmente quando as duas assinaturas pertencerem a organizações diferentes.

Devido à alteração do contexto da assinatura neste exercício, talvez seja mais fácil usar o PowerShell localmente no seu computador em vez de usar o Azure Cloud Shell ao chegar à etapa 8.

### <a name="step-5---create-and-configure-testvnet1"></a>Etapa 5: criar e configurar o TestVNet1

Você deve concluir a [Etapa 1](#Step1) e a [Etapa 2](#Step2) da seção anterior para criar e configurar o TestVNet1 e o Gateway de VPN para TestVNet1. Para essa configuração, você não precisa criar a TestVNet4 da seção anterior, mas, se criá-la, ela não entrará em conflito com estas etapas. Depois de concluir as Etapas 1 e 2, continue com a Etapa 6 para criar a TestVNet5.

### <a name="step-6---verify-the-ip-address-ranges"></a>Etapa 6: verificar os intervalos de endereços IP

É importante certificar-se de que o espaço de endereço IP da nova rede virtual, TestVNet5, não se sobrepõe com nenhum dos seus intervalos de rede virtual ou intervalos de gateway de rede local. Neste exemplo, as redes virtuais podem pertencer a organizações diferentes. Para este exercício, você pode usar os seguintes valores para TestVNet5:

**Valores para TestVNet5:**

* Nome da VNet: TestVNet5
* Grupo de Recursos: TestRG5
* Local: Leste do Japão
* TestVNet5: 10.51.0.0/16 e 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* IP público: VNet5GWIP
* VPNType: RouteBased
* Conexão: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Etapa 7: criar e configurar TestVNet5

Esta etapa deve ser feita no contexto da nova assinatura. Esta parte pode ser executada pelo administrador em uma organização diferente que possui a assinatura.

1. Declare as variáveis. Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração.

   ```azurepowershell-interactive
   $Sub5 = "Replace_With_the_New_Subscription_Name"
   $RG5 = "TestRG5"
   $Location5 = "Japan East"
   $VnetName5 = "TestVNet5"
   $FESubName5 = "FrontEnd"
   $BESubName5 = "Backend"
   $GWSubName5 = "GatewaySubnet"
   $VnetPrefix51 = "10.51.0.0/16"
   $VnetPrefix52 = "10.52.0.0/16"
   $FESubPrefix5 = "10.51.0.0/24"
   $BESubPrefix5 = "10.52.0.0/24"
   $GWSubPrefix5 = "10.52.255.0/27"
   $GWName5 = "VNet5GW"
   $GWIPName5 = "VNet5GWIP"
   $GWIPconfName5 = "gwipconf5"
   $Connection51 = "VNet5toVNet1"
   ```
2. Conecte-se à assinatura 5. Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Verificar as assinaturas da conta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Especifique a assinatura que você deseja usar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName $Sub5
   ```
3. Crie um novo grupo de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG5 -Location $Location5
   ```
4. Crie as configurações de sub-rede para TestVNet5.

   ```azurepowershell-interactive
   $fesub5 = New-AzVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
   $besub5 = New-AzVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
   $gwsub5 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
   ```
5. Crie a TestVNet5.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
   -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
   ```
6. Solicite um endereço IP público.

   ```azurepowershell-interactive
   $gwpip5 = New-AzPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
   -Location $Location5 -AllocationMethod Dynamic
   ```
7. Crie a configuração do gateway.

   ```azurepowershell-interactive
   $vnet5 = Get-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
   $subnet5  = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
   $gwipconf5 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
   ```
8. Crie o gateway de TestVNet5.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
   -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-8---create-the-connections"></a>Etapa 8: criar as conexões

Neste exemplo, como os gateways estão em assinaturas diferentes, dividirmos esta etapa em duas sessões do PowerShell marcadas como [Assinatura 1] e [Assinatura 5].

1. **[Assinatura 1]** Obter o gateway de rede virtual para a Assinatura 1. Entre e conecte-se à Assinatura 1 antes de executar o exemplo abaixo:

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   ```

   Copie a saída dos seguintes elementos e envie para o administrador da Assinatura 5 via email ou outro método.

   ```azurepowershell-interactive
   $vnet1gw.Name
   $vnet1gw.Id
   ```

   Estes dois elementos terão valores semelhantes à seguinte saída de exemplo:

   ```
   PS D:\> $vnet1gw.Name
   VNet1GW
   PS D:\> $vnet1gw.Id
   /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```
2. **[Assinatura 5]** Obter o gateway de rede virtual para a Assinatura 5. Entre e conecte-se à Assinatura 5 antes de executar o exemplo abaixo:

   ```azurepowershell-interactive
   $vnet5gw = Get-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   ```

   Copie a saída dos seguintes elementos e envie para o administrador da Assinatura 1 via email ou outro método.

   ```azurepowershell-interactive
   $vnet5gw.Name
   $vnet5gw.Id
   ```

   Estes dois elementos terão valores semelhantes à seguinte saída de exemplo:

   ```
   PS C:\> $vnet5gw.Name
   VNet5GW
   PS C:\> $vnet5gw.Id
   /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```
3. **[Assinatura 1]** Criar a conexão TestVNet1 para TestVNet5. Nesta etapa, você criará a conexão de TestVNet1 para TestVNet5. A diferença aqui é que não é possível obter $vnet5gw diretamente, porque ele está em uma assinatura diferente. Você precisará criar um novo objeto do PowerShell com os valores comunicados na Assinatura 1 nas etapas acima. Use o exemplo abaixo. Substitua o Nome, a ID e a chave compartilhada por seus próprios valores. O importante é que a chave compartilhada deve corresponder em ambas as conexões. Criar uma conexão pode levar alguns minutos para ser concluída.

   Conecte-se à Assinatura 1 antes de executar o exemplo abaixo:

   ```azurepowershell-interactive
   $vnet5gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet5gw.Name = "VNet5GW"
   $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
   $Connection15 = "VNet1toVNet5"
   New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. **[Assinatura 5]** Criar a conexão TestVNet5 para TestVNet1. Esta etapa é semelhante à etapa mostrada acima, exceto que você está criando a conexão de TestVNet5 para TestVNet1. O mesmo processo de criação de um objeto do PowerShell com base nos valores obtidos na Assinatura 1 se aplica aqui também. Nesta etapa, certifique-se de que as chaves compartilhadas correspondam.

   Conecte-se à Assinatura 5 antes de executar o exemplo abaixo:

   ```azurepowershell-interactive
   $vnet1gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet1gw.Name = "VNet1GW"
   $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
   $Connection51 = "VNet5toVNet1"
   New-AzVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```

## <a name="verify"></a>verificar uma conexão

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="faq"></a>Perguntas frequentes sobre Rede Virtual para Rede Virtual

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Consulte a [Documentação sobre Máquinas Virtuais](https://docs.microsoft.com/azure/) para obter mais informações.
* Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).

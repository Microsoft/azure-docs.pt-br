---
title: 'Configurar o BGP em Gateways de VPN do Azure: Resource Manager: PowerShell | Microsoft Docs'
description: Este artigo mostra como configurar o BGP com Gateways de VPN do Azure usando o Azure Resource Manager e o PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: d7a84bfda06b5db30afff6322c63a056a414357b
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626571"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Como configurar o BGP em Gateways de VPN do Azure usando o PowerShell
Este artigo explica as etapas para habilitar o BGP em uma conexão de VPN Site a Site (S2S) entre locais e uma conexão de VNet para VNet usando o modelo de implantação do Resource Manager e o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-bgp"></a>Sobre o BGP
O BGP é o protocolo de roteamento padrão usado na Internet para a troca de informações de roteamento e acessibilidade entre duas ou mais redes. O BGP habilita os Gateways de VPN do Azure e os dispositivos de VPN locais, chamados de pares no nível de protocolo BGP, ou vizinhos, para trocar "rotas" que informarão ambos os gateways sobre a disponibilidade e a acessibilidade para que esses prefixos percorram os gateways ou os roteadores envolvidos. O BGP também pode habilitar o roteamento de trânsito entre várias redes propagando rotas que um gateway BGP obtém de um par no nível de protocolo BGP para todos os outros pares no nível de protocolo BGP.

Veja [Visão geral de BGP com Gateways de VPN do Azure](vpn-gateway-bgp-overview.md) para obter mais discussões sobre os benefícios do BGP e entender os requisitos técnicos e as considerações do uso de BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Introdução ao BGP em gateways de VPN do Azure

Este artigo orienta você pelas etapas para executar as seguintes tarefas:

* [Parte 1 - Habilitar o BGP em seu gateway de VPN do Azure](#enablebgp)
* Parte 2 - Estabelecer uma conexão entre instalações com BGP
* [Parte 3 - Estabelecer uma conexão VNet para VNet com BGP](#v2vbgp)

Cada parte das instruções constitui um bloco de construção básico para habilitar o BGP em sua conectividade de rede. Se você concluir todas as três partes, criará a topologia conforme mostrado no diagrama a seguir:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Você pode combinar essas partes para criar uma rede de trânsito mais complexa, com saltos múltiplos e que atenda às suas necessidades.

## <a name ="enablebgp"></a>Parte 1 - Configurar o BGP no Gateway de VPN do Azure
As etapas de configuração definem os parâmetros de BGP do gateway de VPN do Azure, conforme mostrado no diagrama a seguir:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar
* Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale os cmdlets do PowerShell do Azure Resource Manager. Para saber mais sobre como instalar os cmdlets do PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). 

### <a name="step-1---create-and-configure-vnet1"></a>Etapa 1 - Criar e configurar VNet1
#### <a name="1-declare-your-variables"></a>1. Declare as variáveis
Para este exercício, começaremos declarando nossa variáveis. O exemplo a seguir declara as variáveis usando os valores para este exercício. Certifique-se de substituir os valores pelos seus próprios ao configurar para a produção. Se você estiver executando as etapas para se familiarizar com esse tipo de configuração, poderá usar essas variáveis. Modifique as variáveis e, em seguida, copie e cole em seu console do PowerShell.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Conectar à sua assinatura do Azure e criar um novo grupo de recursos
Para usar os cmdlets do Gerenciador de Recursos, alterne para o modo do PowerShell. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Criar TestVNet1
O exemplo a seguir cria uma rede virtual denominada TestVNet1 e três sub-redes, uma chamada GatewaySubnet, outra FrontEnd e outra Backend. Ao substituir valores, é importante você sempre nomear sua sub-rede de gateway especificamente como GatewaySubnet. Se você usar outro nome, a criação do gateway falhará.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Etapa 2 - Criar um Gateway de VPN para TestVNet1 com parâmetros de BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Criar as configurações de IP e sub-redes
Solicite um endereço IP público para ser alocado ao gateway que você criará para sua VNet. Você também definirá as configurações necessárias de IP e sub-rede.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Criar o gateway de VPN com o número AS
Crie o gateway de rede virtual para TestVNet1. O BGP exige um gateway de VPN Baseado em Rota, além do parâmetro de adição, -Asn, para definir o Número AS (ASN) como TestVNet1. Se você não definir o parâmetro ASN, ASN 65515 será atribuído. Criar um gateway pode demorar um pouco (30 minutos ou mais para ser concluído).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Obter o endereço IP do par no nível de protocolo BGP do Azure
Depois de criar o gateway, você precisará obter o endereço IP do par no nível de protocolo BGP no Gateway de VPN do Azure. Esse endereço é necessário para configurar o Gateway de VPN do Azure como um par no nível de protocolo BGP para os dispositivos VPN locais.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

O último comando mostra as configurações correspondentes de BGP no Gateway de VPN do Azure; por exemplo:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Depois de criar o gateway, você poderá usar este gateway para estabelecer a conexão entre instalações ou conexão VNet para VNet com BGP. As seções a seguir explicam as etapas para concluir o exercício.

## <a name ="crossprembbgp"></a>Parte 2 - Estabelecer uma conexão entre instalações com BGP

Para estabelecer uma conexão entre instalações, você precisará criar um Gateway de Rede Local para representar o dispositivo VPN local e uma Conexão para conectar o gateway de VPN com ao gateway de rede local. Embora haja artigos para orientar você por meio destas etapas, este artigo contém as propriedades adicionais necessárias para especificar os parâmetros de configuração do BGP.

![BGP para entre instalações](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Antes de prosseguir, verifique se você concluiu a [Parte 1](#enablebgp) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Etapa 1 - Criar e configurar o gateway de rede local

#### <a name="1-declare-your-variables"></a>1. Declare as variáveis

Este exercício continua a compilar a configuração mostrada no diagrama. Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Algumas coisas para observar relacionadas aos parâmetros de gateway de rede:

* O gateway de rede local pode estar no mesmo ou em outro local e grupo de recursos como o gateway de VPN. Este exemplo os mostra em grupos de recursos diferentes em locais diferentes.
* O prefixo que você precisa declarar para o gateway de rede local é o endereço de host do seu endereço IP do par no nível de protocolo BGP em seu dispositivo VPN. Nesse caso, é um prefixo /32 de "10.52.255.254/32".
* Como lembrete, você deve usar diferentes ASNs BGP entre suas redes locais e a VNet do Azure. Se eles forem iguais, você precisará alterar seu ASN VNet se o dispositivo VPN local já usar o ASN para emparelhar com outros vizinhos de BGP.

Antes de continuar, verifique se que você ainda está conectado à Assinatura 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Criar o gateway de rede local para Site5

Certifique-se de criar o grupo de recursos se ele não for criado, antes de criar o gateway de rede local. Observe os dois parâmetros adicionais do gateway de rede local: ASN e BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Etapa 2 - Conectar o gateway de VNet e o gateway de rede local

#### <a name="1-get-the-two-gateways"></a>1. Obter os dois gateways

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Criar a conexão TestVNet1 para Site5

Nesta etapa, você cria a conexão de TestVNet1 para Site5. Você deve especificar "-EnableBGP $True" para habilitar o BGP para essa conexão. Conforme discutido anteriormente, é possível ter conexões BGP e conexões que não são de BGP para o mesmo Gateway de VPN do Azure. A menos que o BGP esteja habilitado na propriedade de conexão, o Azure não habilitará o BGP para essa conexão, mesmo que parâmetros BGP já estejam configurados em ambos os gateways.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

O exemplo a seguir lista os parâmetros que você inserirá na seção de configuração de BGP em seu dispositivo VPN local para este exercício:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A conexão é estabelecida após alguns minutos, e a sessão de emparelhamento via protocolo BGP inicia uma vez estabelecida a conexão IPsec.

## <a name ="v2vbgp"></a>Parte 3 - Estabelecer uma conexão VNet para VNet com BGP

Esta seção adiciona uma conexão de VNet para VNet com BGP, conforme mostrado neste diagrama:

![BGP de VNet para VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

As instruções a seguir continuam das etapas anteriores. Você deve concluir a [Parte 1](#enablebgp) para criar e configurar o Gateway de VPN e TestVNet1 com BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Etapa 1 - Criar TestVNet2 e gateway de VPN

É importante certificar-se de que o espaço de endereço IP da nova rede virtual, TestVNet2, não se sobreponha a nenhum dos seus intervalos de VNet.

Neste exemplo, as redes virtuais pertencem à mesma assinatura. Você pode configurar conexões de rede virtual a rede entre assinaturas diferentes. Para saber mais, veja [Configurar uma conexão VNet com VNet](vpn-gateway-vnet-vnet-rm-ps.md). Verifique se você adicionou "-EnableBgp $True" ao criar conexões para habilitar o BGP.

#### <a name="1-declare-your-variables"></a>1. Declare as variáveis

Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Criar TestVNet2 no novo grupo de recursos

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Criar um gateway de VPN para TestVNet2 com parâmetros de BGP

Solicite um endereço IP público a ser alocada para o gateway, você criará para a sua rede virtual e defina a sub-rede necessária e configurações de IP.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Crie o gateway de VPN com o número AS. Você deve substituir o ASN padrão em seus gateways de VPN do Azure. As ASNs para as VNets conectadas devem ser diferentes para habilitar o BGP e roteamento de tráfego.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Etapa 2 - Conectar os gateways de TestVNet1 e TestVNet2

Neste exemplo, ambos os gateways estão na mesma assinatura. Você pode concluir essa etapa na mesma sessão do PowerShell.

#### <a name="1-get-both-gateways"></a>1. Obter ambos os gateways

Certifique-se de fazer logon e se conectar à Assinatura 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Criar ambas as conexões

Nesta etapa, você cria a conexão de TestVNet1 para TestVNet2 e a conexão de TestVNet2 to TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Certifique-se de habilitar o BGP para AMBAS as conexões.
> 
> 

Depois de concluir estas etapas, a conexão será estabelecida após alguns minutos. A sessão de emparelhamento via protocolo BGP fica ativa até a conclusão da conexão VNET a VNET.

Se você tiver concluído todas as partes deste exercício, estabeleceu a topologia de rede a seguir:

![BGP de VNet para VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter as etapas.

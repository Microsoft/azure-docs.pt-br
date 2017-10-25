---
title: "Conectar gateways VPN do Azure a vários dispositivos VPN com base em políticas locais: Azure Resource Manager: PowerShell | Microsoft Docs"
description: "Este artigo orienta você pela configuração do gateway de VPN com base em rotas do Azure para vários dispositivos VPN com base em políticas usando o Azure Resource Manager e o PowerShell."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/27/2017
ms.author: yushwang
ms.openlocfilehash: db4d8837fb5c5d15364422e957e4914966215674
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Conectar gateways VPN do Azure a vários dispositivos VPN com base em políticas locais usando o PowerShell

Este artigo ajuda você a configurar um gateway de VPN com base em rotas do Azure para se conectar a vários dispositivos VPN com base em políticas locais utilizando políticas de IPsec/IKE personalizadas em conexões VPN S2S.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a>Sobre gateways VPN com base em políticas e rotas

Os dispositivos VPN com base em políticas *x* rotas diferem em como os seletores de tráfego IPsec são definidos em uma conexão:

* Os dispositivos VPN **com base em políticas** usam as combinações de prefixos de ambas as redes para definir como o tráfego será criptografado/descriptografado por meio de túneis IPsec. Normalmente, ele é criado em dispositivos de firewall que executam a filtragem de pacote. A descriptografia e criptografia de túnel IPsec são adicionadas à filtragem de pacote e ao mecanismo de processamento.
* Os dispositivos VPN **com base em rotas** usam seletores de tráfego qualquer (curinga) e permitem roteamento/encaminhamento do tráfego da direção das tabelas para diferentes túneis IPsec. Normalmente, ele é criado em plataformas de roteador, onde cada túnel IPsec é modelado como uma interface de rede ou VTI (interface do túnel virtual).

Os diagramas a seguir realçam dois modelos:

### <a name="policy-based-vpn-example"></a>Exemplo de VPN com base em políticas
![baseado em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Exemplo de VPN com base em rotas
![baseado em rotas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Suporte do Azure para VPN com base em políticas
Atualmente, o Azure oferece suporte a ambos os modos de gateways VPN: gateways VPN com base em rotas e gateways VPN com base em políticas. Eles são criados em diferentes plataformas internas que resultam em especificações diferentes:

|                          | **Gateway de VPN PolicyBased** | **Gateway de VPN RouteBased**               |
| ---                      | ---                         | ---                                      |
| **SKU de gateway do Azure**    | Basic                       | Básico, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **Versão IKE**          | IKEv1                       | IKEv2                                    |
| **IOPS Conexões S2S** | **1**                       | Basic/Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Com a política de IPsec/IKE personalizada, agora você pode configurar gateways VPN com base em rota do Azure para usar os seletores de tráfego com base no prefixo com a opção "**PolicyBasedTrafficSelectors**", para se conectar a dispositivos VPN baseados em políticas locais. Esse recurso permite a conexão de uma rede virtual do Azure e o gateway de VPN para vários dispositivos VPN/firewall com base em políticas locais, removendo o limite de conexão única dos gateways VPN com base em políticas atuais do Azure.

> [!IMPORTANT]
> 1. Para habilitar essa conectividade, seus dispositivos VPN com base em políticas locais devem oferecer suporte a **IKEv2** para conectarem os gateways VPN com base em rotas do Azure. Verifique as especificações do dispositivo VPN.
> 2. As redes locais que se conectam por meio de dispositivos VPN com base em políticas com esse mecanismo só podem se conectar à rede virtual do Azure. **eles não podem transitar por outras redes locais ou redes virtuais por meio do mesmo gateway de VPN do Azure**.
> 3. A opção de configuração faz parte da política de conexão IPsec/IKE personalizada. Se você habilitar a opção do seletor de tráfego com base em políticas, deverá especificar a política toda (algoritmos de criptografia e de integridade de IPsec/IKE, restrições de chave e tempos de vida do SA).

O diagrama a seguir mostra por que o roteamento de tráfego por meio do gateway de VPN do Azure não funcionará com a opção baseada em políticas:

![trânsito com base em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Conforme mostrado no diagrama, o gateway de VPN do Azure terá seletores de tráfego da rede virtual para cada prefixo de rede local, mas não os prefixos de conexão cruzada. Por exemplo, o local 2, local 3 e local 4 podem cada um se comunicar com a VNet1 respectivamente, mas não podem se conectar por meio do gateway de VPN do Azure entre si. O diagrama mostra os seletores de tráfego com conexão cruzada que não estão disponíveis no gateway de VPN do Azure nesta configuração.

## <a name="configure-policy-based-traffic-selectors-on-a-connection"></a>Configurar os seletores de tráfego com base em políticas para uma conexão

As instruções neste artigo seguem o mesmo exemplo, conforme descrito em [Configurar política de IPsec/IKE para conexões S2S ou VNet para VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md), para estabelecer uma conexão VPN S2S. Isso é mostrado no diagrama a seguir:

![política de S2S](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

O fluxo de trabalho para habilitar esta conectividade:
1. Criar a rede virtual, o gateway de VPN e o gateway de rede local para suas conexões locais cruzadas
2. Criar uma política de IPsec/IKE
3. Aplicar a política, quando você cria uma conexão S2S ou VNet para VNet e **habilita os seletores de tráfego com base em políticas** na conexão
4. Se a conexão já foi criada, você poderá aplicar ou atualizar a política para uma conexão existente

## <a name="enable-policy-based-traffic-selectors-on-a-connection"></a>Habilitar os seletores de tráfego com base em políticas para uma conexão

Verifique se você concluiu a [Parte 3 do artigo Configurar a política de IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para esta seção. O exemplo a seguir usa os mesmos parâmetros e etapas:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Etapa 1 - Criar a rede virtual, o gateway de VPN e o gateway de rede local

#### <a name="1-declare-your-variables--connect-to-your-subscription"></a>1. Declare as variáveis e conecte-se à sua assinatura
Para este exercício, começaremos declarando nossa variáveis. Certifique-se de substituir os valores pelos seus próprios ao configurar para a produção.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```
Para usar os cmdlets do Resource Manager, alterne para o modo do PowerShell. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Criar a rede virtual, o gateway de VPN e o gateway de rede local
O exemplo a seguir cria a rede virtual, TestVNet1, com três sub-redes e o gateway de VPN. Ao substituir valores, é importante que você sempre nomeie sua sub-rede de gateway especificamente como ‘GatewaySubnet’. Se você usar outro nome, a criação do gateway falhará.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Parte 2 - Criar uma nova conexão de VPN S2S com a política de IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Criar uma política de IPsec/IKE

> [!IMPORTANT]
> Você precisa criar uma política de IPsec/IKE para habilitar a opção "UsePolicyBasedTrafficSelectors" na conexão.

O exemplo a seguir cria uma política de IPsec/IKE com esses algoritmos e parâmetros:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS24, tempo de vida da SA 3600 segundos e 2048KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Criar a conexão VPN S2S com seletores de tráfego com base em políticas e a política de IPsec/IKE
Crie uma conexão VPN S2S e aplique a política de IPsec/IKE criada na etapa anterior. Observe o parâmetro adicional "-UsePolicyBasedTrafficSelectors $True", que habilita o seletor de tráfego baseado em políticas na conexão.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Depois de concluir as etapas, a conexão VPN S2S usará a política de IPsec/IKE definida e habilita os seletores de tráfego com base em políticas na conexão. Você pode repetir as mesmas etapas para adicionar mais conexões para dispositivos VPN baseados em políticas locais adicionais do mesmo gateway de VPN do Azure.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Atualizar seletores de tráfego com base em políticas para uma conexão
A última seção mostra como atualizar a opção de seletores de tráfego com base em políticas para uma conexão VPN S2S existente.

### <a name="1-get-the-connection"></a>1. Obtenha a conexão
Obtenha o recurso de conexão.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Marque a opção dos seletores de tráfego com base em políticas
A linha a seguir mostra se os seletores de tráfego com base em políticas são usados para a conexão:

```powershell
$connection6.UsePolicyBasedTrafficSelectors
```

Se a linha retornar "**True**", então os seletores de tráfego com base em políticas serão configurados na conexão; caso contrário, ela retorna "**False**".

### <a name="3-update-the-policy-based-traffic-selectors-on-a-connection"></a>3. Atualizar os seletores de tráfego com base em políticas para uma conexão
Depois de obter o recurso de conexão, você pode habilitar ou desabilitar a opção.

#### <a name="disable-usepolicybasedtrafficselectors"></a>Desabilitar UsePolicyBasedTrafficSelectors
O exemplo a seguir desabilita a opção de seletores de tráfego com base na política, mas deixa a política de IPsec/IKE inalterada:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

#### <a name="enable-usepolicybasedtrafficselectors"></a>Habilitar UsePolicyBasedTrafficSelectors
O exemplo a seguir habilita a opção de seletores de tráfego com base na política, mas deixa a política de IPsec/IKE inalterada:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter as etapas.

Também analise [Configurar política de IPsec/IKE para conexões de VPN S2S ou Rede Virtual para Rede Virtual](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter mais detalhes sobre políticas de IPsec/IKE personalizadas.

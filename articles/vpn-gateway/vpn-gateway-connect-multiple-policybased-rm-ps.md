---
title: 'Gateway de VPN do Azure: conectar gateways a vários dispositivos VPN baseados em políticas locais'
description: Configure um gateway VPN do Azure com base em rotas para vários dispositivos VPN com base em políticas usando o Azure Resource Manager e o PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 2a85204fef026940394a19934bef1c631a8e2d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89418867"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Conectar gateways VPN do Azure a vários dispositivos VPN com base em políticas locais usando o PowerShell

Este artigo ajuda você a configurar um gateway de VPN com base em rotas do Azure para se conectar a vários dispositivos VPN com base em políticas locais utilizando políticas de IPsec/IKE personalizadas em conexões VPN S2S.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Sobre gateways de VPN baseados em políticas e baseados em rota

Os dispositivos VPN baseados em políticas *vs.* de rota diferem em como os seletores de tráfego IPSec são definidos em uma conexão:

* **Baseado em políticas** Os dispositivos VPN usam as combinações de prefixos de ambas as redes para definir como o tráfego é criptografado/descriptografado por meio de túneis IPsec. Normalmente, ele é criado em dispositivos de firewall que executam a filtragem de pacote. A criptografia e descriptografia de túnel IPsec são adicionadas à filtragem de pacote e ao mecanismo de processamento.
* **Baseado em rota** Os dispositivos VPN usam seletores de tráfego de qualquer para qualquer (curinga) e permitem que as tabelas de roteamento/encaminhamento direcionem o tráfego para túneis IPsec diferentes. Normalmente, ele é criado em plataformas de roteador, onde cada túnel IPsec é modelado como uma interface de rede ou VTI (interface do túnel virtual).

Os diagramas a seguir realçam dois modelos:

### <a name="policy-based-vpn-example"></a>Exemplo de VPN com base em políticas
![baseado em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Exemplo de VPN com base em rotas
![baseado em rotas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Suporte do Azure para VPN com base em políticas
Atualmente, o Azure oferece suporte a ambos os modos de gateways VPN: gateways VPN com base em rotas e gateways VPN com base em políticas. Eles são criados em diferentes plataformas internas que resultam em especificações diferentes:

| Categoria | Gateway de VPN PolicyBased | Gateway de VPN RouteBased | Gateway de VPN RouteBased |
| -------- | ----------------------- | ---------------------- | ---------------------- |---                                                 |
| **SKU de gateway do Azure**    | Básico                       | Básico                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **Versão do IKE**          | IKEv1                       | IKEv2                            | IKEv1 e IKEv2                         |
| **Máximo de conexões S2S** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Com a política de IPsec/IKE personalizada, agora você pode configurar gateways VPN com base em rota do Azure para usar os seletores de tráfego com base no prefixo com a opção "**PolicyBasedTrafficSelectors**", para se conectar a dispositivos VPN baseados em políticas locais. Esse recurso permite a conexão de uma rede virtual do Azure e o gateway de VPN para vários dispositivos VPN/firewall com base em políticas locais, removendo o limite de conexão única dos gateways VPN com base em políticas atuais do Azure.

> [!IMPORTANT]
> 1. Para habilitar essa conectividade, seus dispositivos VPN com base em políticas locais devem oferecer suporte a **IKEv2** para conectarem os gateways VPN com base em rotas do Azure. Verifique as especificações do dispositivo VPN.
> 2. As redes locais que se conectam por meio de dispositivos VPN com base em políticas com esse mecanismo só podem se conectar à rede virtual do Azure. **eles não podem transitar por outras redes locais ou redes virtuais por meio do mesmo gateway de VPN do Azure**.
> 3. A opção de configuração faz parte da política de conexão IPsec/IKE personalizada. Se você habilitar a opção do seletor de tráfego com base em políticas, deverá especificar a política toda (algoritmos de criptografia e de integridade de IPsec/IKE, restrições de chave e tempos de vida do SA).

O diagrama a seguir mostra por que o roteamento de tráfego por meio do gateway de VPN do Azure não funcionará com a opção baseada em políticas:

![trânsito com base em políticas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Conforme mostrado no diagrama, o gateway de VPN do Azure terá seletores de tráfego da rede virtual para cada prefixo de rede local, mas não os prefixos de conexão cruzada. Por exemplo, o local 2, local 3 e local 4 podem cada um se comunicar com a VNet1 respectivamente, mas não podem se conectar por meio do gateway de VPN do Azure entre si. O diagrama mostra os seletores de tráfego com conexão cruzada que não estão disponíveis no gateway de VPN do Azure nesta configuração.

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

As instruções neste artigo seguem o mesmo exemplo, conforme descrito em [Configurar política de IPsec/IKE para conexões S2S ou VNet para VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md), para estabelecer uma conexão VPN S2S. Isso é mostrado no diagrama a seguir:

![política de S2S](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

O fluxo de trabalho para habilitar esta conectividade:
1. Crie a rede virtual, o gateway de VPN e o gateway de rede local para sua conexão entre locais.
2. Crie uma política de IPsec/IKE.
3. Aplique a política ao criar uma conexão S2S ou VNet a VNet e **habilite os seletores de tráfego baseados em políticas** na conexão.
4. Se a conexão já tiver sido criada, você poderá aplicar ou atualizar a política para uma conexão existente.

## <a name="before-you-begin"></a>Antes de começar

* Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Habilitar seletores de tráfego com base em políticas

Esta seção mostra como habilitar os seletores de tráfego baseados em políticas em uma conexão. Verifique se você concluiu [a parte 3 do artigo configurar a política de IPSec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md). As etapas neste artigo usam os mesmos parâmetros.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Etapa 1 - Criar a rede virtual, o gateway de VPN e o gateway de rede local

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Conectar a assinatura e declarar as variáveis

1. Se você estiver executando o PowerShell localmente em seu computador, entre usando o cmdlet *Connect-AzAccount* . Ou, em vez disso, use Azure Cloud Shell em seu navegador.

2. Declare as variáveis. Para este exercício, usamos as seguintes variáveis:

   ```azurepowershell-interactive
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

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Criar a rede virtual, o gateway de VPN e o gateway de rede local

1. Crie um grupos de recursos.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Use o exemplo a seguir para criar a rede virtual TestVNet1 com três sub-redes e o gateway de VPN. Se você quiser substituir valores, é importante sempre nomear sua sub-rede de gateway especificamente como 'GatewaySubnet'. Se você usar outro nome, a criação do gateway falhará.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Etapa 2-criar uma conexão VPN S2S com uma política de IPsec/IKE

1. Crie uma política de IPsec/IKE.

   > [!IMPORTANT]
   > Você precisa criar uma política de IPsec/IKE para habilitar a opção "UsePolicyBasedTrafficSelectors" na conexão.

   O exemplo a seguir cria uma política de IPsec/IKE com esses algoritmos e parâmetros:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS Nenhum, Tempo de Vida da SA de 14400 segundos e 102400000 KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Crie a conexão VPN S2S com seletores de tráfego baseados em políticas e com a política de IPsec/IKE e aplique a política de IPsec/IKE criada na etapa anterior. Lembre-se do parâmetro adicional "-UsePolicyBasedTrafficSelectors $True", que habilita os seletores de tráfego baseados em políticas na conexão.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. Depois de concluir as etapas, a conexão VPN S2S usará a política de IPsec/IKE definida e habilita os seletores de tráfego com base em políticas na conexão. Você pode repetir as mesmas etapas para adicionar mais conexões para dispositivos VPN baseados em políticas locais adicionais do mesmo gateway de VPN do Azure.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Para atualizar seletores de tráfego com base em políticas
Esta seção mostra como atualizar a opção de seletores de tráfego com base em políticas para uma conexão VPN S2S existente.

1. Obtenha o recurso de conexão.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Exiba a opção de seletores de tráfego com base em políticas.
A linha a seguir mostra se os seletores de tráfego com base em políticas são usados para a conexão:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Se a linha retornar "**True**", então os seletores de tráfego com base em políticas serão configurados na conexão; caso contrário, ela retorna "**False**".
1. Depois de obter o recurso de conexão, você pode habilitar ou desabilitar os seletores de tráfego com base em políticas em uma conexão.

   - Para habilitar

      O exemplo a seguir habilita a opção de seletores de tráfego com base na política, mas deixa a política de IPsec/IKE inalterada:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Para desabilitar

      O exemplo a seguir desabilita a opção de seletores de tráfego com base na política, mas deixa a política de IPsec/IKE inalterada:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/windows/quick-create-portal.md) para obter as etapas.

Também analise [Configurar política de IPsec/IKE para conexões de VPN S2S ou Rede Virtual para Rede Virtual](vpn-gateway-ipsecikepolicy-rm-powershell.md) para obter mais detalhes sobre políticas de IPsec/IKE personalizadas.

---
title: 'Azure ExpressRoute: configurar a VPN S2S sobre o emparelhamento da Microsoft'
description: Configure a conectividade de IPsec/IKE para o Azure por um circuito de emparelhamento Microsoft para ExpressRoute usando um gateway VPN site a site.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 366f27a0e2a22e9aa10dda20e105bf644255bdd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393132"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Configurar uma VPN site a site no emparelhamento da Microsoft para ExpressRoute

Este artigo ajuda você a configurar uma conectividade criptografada segura entre sua rede local e suas redes virtuais do Azure (VNets) sobre uma conexão privada de ExpressRoute. É possível usar o emparelhamento da Microsoft para estabelecer um túnel VPN de IPsec/IKE site a site entre suas redes locais selecionadas e VNets do Azure. Configurar um túnel seguro por meio do ExpressRoute permite a troca de dados com confidencialidade, contra reprodução, autenticidade e integridade.

>[!NOTE]
>Ao configurar a VPN site a site em emparelhamento da Microsoft, você é cobrado pelo gateway de VPN e pela saída de VPN. Para obter mais informações, consulte [preços do gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Arquitetura


  ![visão geral de conectividade](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Para alta disponibilidade e redundância, você pode configurar vários túneis em dois pares de MSEE-PE de um circuito de ExpressRoute e habilitar o balanceamento de carga entre os túneis.

  ![opções de alta disponibilidade](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Túneis VPN em emparelhamento da Microsoft podem ser terminados usando gateway de VPN ou usando uma Solução de Virtualização de Rede (NVA) disponível por meio do Azure Marketplace. Você pode trocar rotas estática ou dinamicamente nos túneis criptografados sem expor a troca de rota para o emparelhamento da Microsoft subjacente. Nos exemplos neste artigo, BGP (diferente da sessão BGP usada para criar o emparelhamento da Microsoft) é usado para trocar dinamicamente prefixos nos túneis criptografados.

>[!IMPORTANT]
>Para o lado local, normalmente o emparelhamento da Microsoft é terminado no DMZ e o emparelhamento privado é terminado na zona de rede principal. As duas zonas seriam segregadas usando firewalls. Se você estiver configurando o emparelhamento da Microsoft exclusivamente para habilitar o túnel seguro por meio do ExpressRoute, lembre-se de filtrar apenas os IPs públicos de interesse que estão sendo anunciados por meio do emparelhamento da Microsoft.
>
>

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

1. Configure o emparelhamento da Microsoft para seu circuito de ExpressRoute.
2. Anuncie prefixos de públicos regionais do Azure selecionados para sua rede local por meio de emparelhamento da Microsoft.
3. Configurar um gateway de VPN e estabelecer encapsulamentos IPsec
4. Configure o dispositivo VPN local.
5. Crie a conexão IPsec/IKE site a site.
6. (Opcional) Configure firewalls/filtragem no dispositivo VPN local.
7. Teste e valide a comunicação do IPsec sobre o circuito de ExpressRoute.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. configurar o emparelhamento da Microsoft

Para configurar uma conexão de VPN site a site em ExpressRoute, você deve aproveitar o emparelhamento da Microsoft do ExpressRoute.

* Para configurar um novo circuito de ExpressRoute, inicie com o artigo [pré-requisitos do ExpressRoute](expressroute-prerequisites.md) e, em seguida, [Criar e modificar um circuito de ExpressRoute](expressroute-howto-circuit-arm.md).

* Se você já tiver um circuito de ExpressRoute, mas não tiver configurado um emparelhamento da Microsoft, configure o emparelhamento da Microsoft usando o artigo [Criar e modificar o emparelhamento de um circuito de ExpressRoute](expressroute-howto-routing-arm.md#msft).

Depois que tiver configurado seu circuito e emparelhamento da Microsoft, você pode facilmente exibi-lo usando a página **Visão geral** no Portal do Azure.

![circuito](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. configurar filtros de rota

Um filtro de rota permite identificar os serviços que você deseja consumir por meio de emparelhamento da Microsoft do seu circuito de ExpressRoute. É essencialmente uma lista de permissões de todos os valores de comunidade BGP. 

![filtro de rota](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

Neste exemplo, a implantação é apenas na região *Azure Oeste dos EUA 2*. Uma regra de filtro de rota é adicionada para permitir somente o anúncio de prefixos regionais Azure Oeste dos EUA 2, que têm o valor de comunidade BGP *12076:51026*. Especifique os prefixos regionais que você deseja permitir selecionando **Gerenciar regra**.

Dentro do filtro de rota, você também precisa escolher os circuitos de ExpressRoute para o qual o filtro de rota se aplica. Você pode escolher os circuitos de ExpressRoute selecionando **Adicionar circuito**. Na figura anterior, o filtro de rota está associado ao exemplo de circuito de ExpressRoute.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2.1 Configurar o filtro de rota

Configure um filtro de rota. Para etapas, consulte [Configurar os filtros de rota para emparelhamento da Microsoft](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2.2 Verificar rotas BGP

Depois de ter criado com êxito o emparelhamento da Microsoft em seu circuito de ExpressRoute e associado um filtro de rota ao circuito, você pode verificar as rotas de BGP recebidas dos MSEEs nos dispositivos PE que estão emparelhados com o MSEEs. O comando de verificação varia, dependendo do sistema operacional de seus dispositivos PE.

#### <a name="cisco-examples"></a>Exemplos da Cisco

Este exemplo usa um comando Cisco IOS-XE. No exemplo, um roteamento virtual e o encaminhamento (VRF) de instância é usado para isolar o tráfego de emparelhamento.

```
show ip bgp vpnv4 vrf 10 summary
```

A saída parcial a seguir mostra que os prefixos 68 foram recebidos do vizinho \* . 243.229.34 com o ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Para ver a lista de prefixos recebidos do vizinho, use o exemplo a seguir:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Para confirmar que está recebendo o conjunto correto de prefixos, você pode realizar uma verificação cruzada. A saída de comando do PowerShell do Azure a seguir lista os prefixos anunciados por meio do emparelhamento da Microsoft para cada um dos serviços e para cada região do Azure:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. configurar o gateway de VPN e os túneis IPsec

Nesta seção, são criados os túneis VPN IPsec entre o gateway de VPN do Azure e o dispositivo VPN local. Os exemplos usam dispositivos VPN de roteador de serviço de nuvem da Cisco (CSR1000).

O diagrama a seguir mostra os túneis de VPN IPsec estabelecidos entre o dispositivo VPN local 1 e o par de instâncias de gateway de VPN do Azure. Os dois túneis de VPN IPsec estabelecidos entre o dispositivo VPN local 2 e o par de instâncias de gateway de VPN do Azure não são ilustrados no diagrama e os detalhes de configuração não estão listados. No entanto, ter túneis VPN adicionais melhora a alta disponibilidade.

  ![Túneis VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Sobre o par de túneis IPsec, é estabelecida uma sessão eBGP para troca de rotas de rede privada. O diagrama a seguir mostra a sessão eBGP estabelecida sobre o par de túneis IPsec:

  ![sessões eBGP por par de túnel](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

O diagrama a seguir mostra a visão geral abstraída da rede de exemplo:

  ![exemplo de rede](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Sobre os exemplos de modelo do Azure Resource Manager

Nos exemplos, o gateway de VPN e as terminações de túnel IPsec são configurados usando um modelo de Azure Resource Manager. Se você for novo no uso de modelos do Resource Manager ou para compreender os fundamentos de modelo do Resource Manager, consulte [Entender a estrutura e a sintaxe de modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). O modelo nesta seção cria um novo ambiente do Azure (VNet). No entanto, se você tiver uma rede virtual existente, poderá referenciá-la no modelo. Se você não estiver familiarizado com as configurações de site a site de IPsec/IKE de gateway de VPN, consulte [Criar uma conexão site a site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Você não precisa usar modelos do Azure Resource Manager para criar esta configuração. Você pode criar essa configuração usando o Portal do Azure ou o PowerShell.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3.1 Declarar as variáveis

Neste exemplo, as declarações de variáveis correspondem à rede de exemplo. Ao declarar variáveis, modifique esta seção para refletir seu ambiente.

* A variável **localAddressPrefix** é uma matriz de endereços IP locais para terminar os túneis de IPsec.
* O **gatewaySku** determina a taxa de transferência de VPN. Para obter mais informações sobre tipos de VPN Gateway, confira [Sobre definições de configuração de Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Para preços, consulte [Preços do Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Configure o **vpnType** para **RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3.2 Criar rede virtual (VNet)

Se você estiver associando a uma VNet com os túneis de VPN, pode ignorar esta etapa.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3.3 Atribuir endereços IP públicos a instâncias de gateway de VPN
 
Atribua um endereço IP público para cada instância de um gateway de VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3.4 Especifique a terminação de túnel de VPN local (gateway de rede local)

Os dispositivos VPN locais são chamados de **gateway de rede local**. O snippet json a seguir também especifica os detalhes de emparelhamento BGP remoto:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3.5 Criar o gateway de VPN

Esta seção do modelo configura o gateway de VPN com as configurações necessárias para uma configuração ativo-ativo. Tenha em mente os seguintes requisitos:

* Crie o gateway VPN com um **"RouteBased"** VpnType. Essa configuração é obrigatória se você deseja habilitar o roteamento de BGP entre o gateway de VPN e a VPN no local.
* Para estabelecer túneis de VPN entre as duas instâncias do gateway de VPN e um dispositivo local especificado no modo ativo-ativo, o parâmetro **"activeActive"** é definido como **true** no modelo do Gerenciador de Recursos. Para entender mais sobre gateways de VPN altamente disponíveis, consulte [Conectividade de gateway de VPN altamente disponível](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Para configurar as sessões eBGP entre os túneis de VPN, você deve especificar dois ASNs diferentes em ambos os lados. É preferível especificar números ASN privados. Para obter mais informações, consulte [Visão geral de gateways de VPN do BGP e Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3.6 Estabelecer os túneis de IPsec

A ação final do script cria túneis de IPsec entre o gateway de VPN do Azure e o dispositivo VPN local.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. configurar o dispositivo VPN local

O gateway de VPN do Azure é compatível com vários dispositivos VPN de fornecedores diferentes. Para obter informações de configuração e dispositivos que foram validados para trabalhar com o gateway de VPN, consulte [Sobre dispositivos VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Ao configurar seu dispositivo VPN, você precisará dos itens a seguir:

* Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN site a site. Os exemplos usam uma chave compartilhada básica. Recomendamos gerar uma chave mais complexa para uso.
* O endereço IP público de seu gateway de VPN. Você pode exibir o endereço IP público usando o portal do Azure, o PowerShell ou a CLI. Para localizar o endereço IP público do seu gateway de VPN usando o portal do Azure, navegue até Gateways de rede virtual e clique no nome do seu gateway.

Normalmente pares de eBGP estão diretamente conectados (geralmente por meio de uma conexão de WAN). No entanto, quando você estiver configurando eBGP por túneis de VPN IPsec por meio do emparelhamento de ExpressRoute da Microsoft, há vários domínios de roteamentos entre os pares de eBGP. Use o comando **ebgp-multihop** para estabelecer a relação de vizinho do eBGP entre os dois pares conectados não diretamente. O número inteiro que segue o comando ebgp-multihop especifica o valor TTL nos pacotes de BGP. O comando **maximum-paths eibgp 2** habilita o balanceamento de carga de tráfego entre os dois caminhos de BGP.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Exemplo do Cisco CSR1000

O exemplo a seguir mostra a configuração para Cisco CSR1000 em uma máquina virtual de Hyper-V como um dispositivo VPN local:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. configurar firewalls e filtragem de dispositivo VPN (opcional)

Configure o firewall e a filtragem de acordo com seus requisitos.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. testar e validar o túnel IPsec

O status de túneis de IPsec pode ser verificado no gateway de VPN do Azure por comandos do Powershell:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Saída de exemplo:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Para verificar o status de túneis em instâncias de gateway de VPN do Azure de forma independente, use o exemplo a seguir:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Saída de exemplo:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Você também pode verificar o status de túneis em seu dispositivo VPN local.

Exemplo de Cisco CSR1000:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Saída de exemplo:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

O protocolo de linha na Interface de Túnel Virtual (VTI) não será alterada para "up" até que a fase 2 do IKE tenha sido concluída. O comando a seguir verifica a associação de segurança:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>Verificar a conectividade de ponta a ponta entre a rede interna local e a VNet do Azure

Se os túneis do IPsec estão funcionando e se as rotas estáticas estão configuradas corretamente, você deve ser capaz de efetuar ping no endereço IP do par de BGP remoto:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>Verificar as sessões de BGP por meio de IPsec

No gateway de VPN do Azure, verifique o status do par de BGP:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Saída de exemplo:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Para verificar a lista de prefixos de rede recebidos pelo eBGP dos concentradores de VPN locais, você pode filtrar por "Origem" do atributo:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

No exemplo de saída, o ASN 65010 é o número de sistema autônomo BGP em VPN local.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Para ver a lista de rotas anunciadas:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Saída de exemplo:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Exemplo para o Cisco CSR1000 local:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

A lista de redes anunciado no Cisco CSR1000 local para o gateway de VPN do Azure pode ser listada usando o comando a seguir:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Próximas etapas

* [Configurar o Monitor de Desempenho de Rede para ExpressRoute](how-to-npm.md)

* [Adicionar uma conexão site a site a uma VNet com uma conexão de gateway de VPN existente](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)

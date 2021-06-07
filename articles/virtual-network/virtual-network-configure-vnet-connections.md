---
title: Configurar e validar conexões VPN ou de rede virtual
description: Diretrizes passo a passo para configurar e validar várias implantações de rede virtual e VPN do Azure
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 9a0d06a8f8fa8f68f063404f2b483b817eb0563f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452094"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Configurar e validar conexões VPN ou de rede virtual

Este tutorial fornece orientações passo a passo para configurar e validar várias implantações de rede virtual e VPN do Azure. Os cenários incluem roteamento de trânsito, conexões de rede para rede, Border Gateway Protocol (BGP), conexões multissite e conexões ponto a site.

Os gateways de VPN do Azure habilitam a flexibilidade na organização de praticamente qualquer tipo de topologia de rede virtual conectada no Azure. Por exemplo, você pode conectar redes virtuais:

- Entre regiões.
- Entre os tipos de rede virtual (Azure Resource Manager versus clássico).
- No Azure ou em um ambiente híbrido local.
- Em assinaturas diferentes. 

## <a name="network-to-network-vpn-connection"></a>Conexão VPN de rede para rede

Conectar uma rede virtual a outra rede virtual (rede a rede) via VPN é semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro por meio de IPsec e IKE. As redes virtuais podem estar na mesma região ou em regiões diferentes, e com a mesma assinatura ou em assinaturas diferentes.

![Conexão de rede para rede com IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Se suas redes virtuais estiverem na mesma região, talvez você queira considerar conectá-las usando o emparelhamento de rede virtual. O emparelhamento de rede virtual não usa um gateway de VPN. Ele aumenta a taxa de transferência e diminui a latência. Para configurar uma conexão de emparelhamento de rede virtual, selecione **configurar e validar emparelhamento VNet**.

Se suas redes virtuais foram criadas por meio do modelo de implantação do Azure Resource Manager, selecione **configurar e validar uma vnet do Resource Manager para uma conexão vnet do Gerenciador de recursos** para configurar uma conexão VPN.

Se uma das redes virtuais foi criada por meio do modelo de implantação clássico do Azure e a outra foi criada por meio do Resource Manager, selecione **configurar e validar uma vnet clássica para uma conexão vnet do Gerenciador de recursos** para configurar uma conexão VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Configurar o emparelhamento de rede virtual para duas redes virtuais na mesma região

Antes de começar a implementar e configurar o emparelhamento de rede virtual do Azure, certifique-se de atender aos seguintes pré-requisitos:

* As redes virtuais emparelhadas devem existir na mesma região do Azure.
* As redes virtuais emparelhadas devem ter espaços de endereço IP que não se sobrepõem.
* O emparelhamento de rede virtual é entre duas redes virtuais. Não há nenhuma relação transitiva derivada entre emparelhamentos. Por exemplo, se VNetA estiver emparelhado com VNetB e VNetB estiver emparelhado com VNetC, VNetA *não* será emparelhado com VNetC.

Ao atender aos requisitos, você pode seguir [o tutorial: conectar redes virtuais com o emparelhamento de rede virtual usando o portal do Azure](./tutorial-connect-virtual-networks-portal.md) para criar e configurar o emparelhamento.

Para verificar a configuração de emparelhamento, use o seguinte método:

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta que tenha as [funções e permissões](virtual-network-manage-peering.md#permissions)necessárias.
2. Na caixa que contém o texto **Pesquisar recursos** na parte superior do portal, digite **redes virtuais**. Quando os resultados da pesquisa exibirem **Redes virtuais**, selecione essa opção.
3. Na folha **redes virtuais** que aparece, selecione a rede virtual para a qual você deseja criar um emparelhamento.
4. No painel que aparece para a rede virtual, selecione **emparelhamentos** na seção **configurações** .
5. Selecione um emparelhamento e exiba os resultados da configuração.

![Seleções para verificar a configuração de emparelhamento de rede virtual](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Para Azure PowerShell, execute o comando [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) para obter o emparelhamento de rede virtual. Veja um exemplo:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Conectar uma rede virtual do Resource Manager a outra rede virtual do Resource Manager

Você pode configurar uma conexão de uma rede virtual do Gerenciador de recursos para outra rede virtual do Resource Manager diretamente. Ou você pode configurar a conexão usando o IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Configurar uma conexão VPN entre as redes virtuais do Gerenciador de recursos

Para configurar uma conexão entre as redes virtuais do Gerenciador de recursos sem IPsec, consulte [Configurar uma conexão de gateway de VPN de rede para rede usando o portal do Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

Para configurar uma conexão com IPsec entre duas redes virtuais do Gerenciador de recursos, siga as etapas 1 a 5 em [criar uma conexão site a site no portal do Azure](../vpn-gateway/tutorial-site-to-site-portal.md) para cada rede virtual.

> [!Note]
> Essas etapas funcionam apenas para redes virtuais na mesma assinatura. Se suas redes virtuais estiverem em assinaturas diferentes, você deverá usar o PowerShell para fazer a conexão. Consulte o artigo [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Validar a conexão VPN entre as redes virtuais do Gerenciador de recursos

![Conexão de rede virtual clássica para uma rede virtual Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Para verificar se a conexão VPN está configurada corretamente, siga estas instruções.

> [!Note] 
> Os números após os componentes de rede virtual nessas etapas correspondem aos números no diagrama anterior.

1. Verifique se não há espaços de endereço sobrepostos nas redes virtuais conectadas.
2. Verifique se o intervalo de endereços para a rede virtual Azure Resource Manager (1) está definido com precisão na instância do **objeto de conexão** (4).
3. Verifique se o intervalo de endereços para a rede virtual de Azure Resource Manager (6) está definido com precisão na instância do **objeto de conexão** (3).
4. Verifique se as chaves pré-compartilhadas estão combinando nos objetos de conexão.
5. Verifique se o VIP do gateway de rede virtual Azure Resource Manager (2) está definido com precisão na instância do **objeto de conexão** (4).
6. Verifique se o VIP do gateway de rede virtual Azure Resource Manager (5) está definido com precisão na instância do **objeto de conexão** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Conectar uma rede virtual clássica a uma rede virtual do Resource Manager

Você pode criar uma conexão entre redes virtuais que estão em assinaturas diferentes e em regiões diferentes. Você também pode conectar redes virtuais que já têm conexões com redes locais, desde que você tenha configurado o tipo de gateway como baseado em rota.

Para configurar uma conexão entre uma rede virtual clássica e uma rede virtual do Resource Manager, consulte [conectar redes virtuais de diferentes modelos de implantação usando o portal do Azure](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

![Diagrama que mostra uma conexão de rede virtual clássica a uma rede virtual Azure Resource Manager.](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Para verificar a configuração ao conectar uma rede virtual clássica a uma Azure Resource Manager rede virtual, siga estas instruções.

> [!Note] 
> Os números após os componentes de rede virtual nessas etapas correspondem aos números no diagrama anterior. 

1. Verifique se não há espaços de endereço sobrepostos nas redes virtuais conectadas.
2. Verifique se o intervalo de endereços para a rede virtual de Azure Resource Manager (6) está definido com precisão na definição de rede local clássica (3).
3. Verifique se o intervalo de endereços da rede virtual clássica (1) está definido com precisão na instância do **objeto de conexão** Azure Resource Manager (4).
4. Verifique se o VIP (2) do gateway de rede virtual clássica está definido com precisão na instância do **objeto de conexão** Azure Resource Manager (4).
5. Verifique se o gateway de rede virtual Azure Resource Manager (5) está definido com precisão na instância de **definição de rede local** clássica (3).
6. Verifique se as chaves pré-compartilhadas estão combinando em ambas as redes virtuais conectadas:
   - Rede virtual clássica: **definição de rede local** (3)
   - Rede virtual Azure Resource Manager: **objeto de conexão** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Criar uma conexão VPN ponto a site

Uma configuração ponto a site (*P2S* no diagrama a seguir) permite que você crie uma conexão segura de um computador cliente individual para uma rede virtual. As conexões ponto a site são úteis quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência. Eles também são úteis quando você tem apenas alguns clientes que precisam se conectar a uma rede virtual. 

A conexão VPN ponto a site é iniciada no computador cliente por meio do cliente VPN do Windows nativo. Os clientes de conexão utilizam certificados para autenticação.

![Conexão ponto a site](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

As conexões ponto a site não exigem um dispositivo VPN. Eles criam a conexão VPN sobre SSTP (Secure Socket encapsulating Protocol). Você pode conectar uma conexão ponto a site a uma rede virtual usando várias ferramentas de implantação e modelos de implantação:

* [Configurar uma conexão ponto a site com uma rede virtual usando o portal do Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Configurar uma conexão ponto a site com uma rede virtual usando o portal do Azure (clássico)](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)
* [Configurar uma conexão ponto a site com uma rede virtual usando o PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="validate-your-point-to-site-connection"></a>Validar sua conexão ponto a site

O artigo [solução de problemas: problemas de conexão de ponto a site do Azure](../vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) percorre problemas comuns com conexões ponto a site.

## <a name="create-a-multisite-vpn-connection"></a>Criar uma conexão VPN multissite

Você pode adicionar uma conexão site a site (*S2S* no diagrama a seguir) a uma rede virtual que já tenha uma conexão site a site, uma conexão ponto a site ou uma conexão de rede para rede. Esse tipo de conexão é geralmente chamado de configuração *multissite* . 

![Conexão multissite](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

O Azure funciona atualmente com dois modelos de implantação: o Gerenciador de recursos e o clássico. Os dois modelos não são totalmente compatíveis entre si. Para configurar uma conexão multissite com modelos diferentes, consulte os seguintes artigos:

* [Adicionar uma conexão site a site a uma rede virtual com uma conexão de gateway de VPN existente](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
* [Adicionar uma conexão site a site a uma rede virtual com uma conexão de gateway de VPN existente (clássica)](../vpn-gateway/vpn-gateway-multi-site.md)

> [!Note]
> As etapas nesses artigos não se aplicam às configurações de conexão coexistentes do Azure ExpressRoute e site a site. Para obter mais informações, consulte [ExpressRoute e conexões coexistentes site a site](../expressroute/expressroute-howto-coexist-resource-manager.md).

## <a name="configure-transit-routing"></a>Configurar roteamento de trânsito

O roteamento de trânsito é um cenário de roteamento específico em que você conecta várias redes em uma topologia de rede de Margarida. Esse roteamento permite que os recursos em redes virtuais em qualquer extremidade da cadeia se comuniquem entre si por meio de redes virtuais entre elas. Sem roteamento de trânsito, redes ou dispositivos emparelhados por meio de um Hub não podem alcançar um ao outro.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Configurar o roteamento de trânsito em uma conexão ponto a site

Imagine um cenário no qual você deseja configurar uma conexão VPN site a site entre VNetA e VNetB. Você também deseja configurar uma VPN ponto a site para o cliente se conectar ao gateway de VNetA. Em seguida, você deseja habilitar o roteamento de trânsito para os clientes ponto a site se conectarem ao VNetB, que passa pelo VNetA. 

Esse cenário tem suporte quando o BGP está habilitado na VPN site a site entre VNetA e VNetB. Para obter mais informações, consulte [sobre o roteamento de VPN ponto a site](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Configurar o roteamento de trânsito em uma conexão do ExpressRoute

O Azure ExpressRoute permite que você estenda suas redes locais para a nuvem da Microsoft por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade. Com o ExpressRoute, é possível estabelecer conexões com os serviços em nuvem da Microsoft, como Microsoft Azure, Microsoft 365 e Dynamics 365. Para obter mais informações, confira [Visão geral do ExpressRoute](../expressroute/expressroute-introduction.md).

![Conexão de emparelhamento privado do ExpressRoute para redes virtuais do Azure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Recomendamos que, se VNetA e VNetB estiverem na mesma região de geopolítica, você [vincular ambas as redes virtuais ao circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md) em vez de configurar o roteamento de trânsito. Se suas redes virtuais estiverem em regiões geopolítica diferentes, você também poderá vinculá-las ao seu circuito diretamente se tiver o [ExpressRoute Premium](../expressroute/expressroute-faqs.md#expressroute-premium). 

Se você tiver a coexistência de ExpressRoute e site a site, o roteamento de trânsito não terá suporte. Para obter mais informações, consulte [Configurar o ExpressRoute e site a site usando o PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md).

Se você tiver habilitado o ExpressRoute para conectar suas redes locais a uma rede virtual do Azure, poderá habilitar o emparelhamento entre as redes virtuais em que você deseja ter roteamento de trânsito. Para permitir que suas redes locais se conectem à rede virtual remota, você deve configurar o [emparelhamento de rede virtual](./virtual-network-peering-overview.md#gateways-and-on-premises-connectivity). 

> [!Note]
> O emparelhamento de rede virtual está disponível somente para redes virtuais na mesma região.

Para verificar se você configurou o roteamento de trânsito para o emparelhamento de rede virtual, siga estas instruções:

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta que tenha as [funções e permissões](virtual-network-manage-peering.md#permissions)necessárias.
2. [Crie um emparelhamento entre VNetA e VNetB](./tutorial-connect-virtual-networks-portal.md) , conforme mostrado no diagrama anterior. 
3. No painel que aparece para a rede virtual, selecione **emparelhamentos** na seção **configurações** .
4. Selecione o emparelhamento que você deseja exibir. Em seguida, selecione **configuração** para validar que você habilitou **permitir** que o gateway esteja em trânsito na rede VNetA conectada ao circuito do Expressroute e **use o gateway remoto** na rede VNetB remota não conectada ao circuito do expressroute.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Configurar o roteamento de trânsito em uma conexão de emparelhamento de rede virtual

Quando as redes virtuais estiverem emparelhadas, você também poderá configurar o gateway na rede virtual emparelhada como um ponto de trânsito para uma rede local. Para configurar uma rota de trânsito no emparelhamento de rede virtual, consulte conexões de rede [para rede](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!Note]
> O tráfego de gateway não tem suporte na relação de emparelhamento entre redes virtuais criadas por meio de modelos de implantação diferentes. Ambas as redes virtuais na relação de emparelhamento devem ter sido criadas por meio do Gerenciador de recursos para que o gateway funcione.

Para verificar se você configurou uma rota de trânsito para o emparelhamento de rede virtual, siga estas instruções:

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta que tenha as [funções e permissões](virtual-network-manage-peering.md#permissions)necessárias.
2. Na caixa que contém o texto **Pesquisar recursos** na parte superior do portal, digite **redes virtuais**. Quando os resultados da pesquisa exibirem **Redes virtuais**, selecione essa opção.
3. Na folha **redes virtuais** que aparece, selecione a rede virtual para a qual você deseja verificar a configuração de emparelhamento.
4. No painel que aparece para a rede virtual que você selecionou, selecione **emparelhamentos** na seção **configurações** .
5. Selecione o emparelhamento que você deseja exibir. Valide se você habilitou **Permitir trânsito de gateway** e **usa gateways remotos** em **configuração**.

![Seleções para verificar se você configurou uma rota de trânsito para o emparelhamento de rede virtual](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Configurar o roteamento de trânsito em uma conexão de rede para rede

Para configurar o roteamento de trânsito entre redes virtuais, você deve habilitar o BGP em todas as conexões de rede para rede intermediárias usando o modelo de implantação do Gerenciador de recursos e o PowerShell. Para obter instruções, consulte [como configurar o BGP em gateways de VPN do Azure usando o PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

O tráfego de trânsito por meio de gateways de VPN do Azure é possível por meio do modelo de implantação clássico, mas isso depende de espaços de endereço estaticamente definidos no arquivo de configuração de rede. O BGP ainda não tem suporte com redes virtuais do Azure e gateways de VPN por meio do modelo de implantação clássico. Sem o BGP, definir manualmente espaços de endereço de trânsito é propenso a erros e não é recomendável.

> [!Note]
> Configure conexões de rede para rede clássicas usando o portal clássico do Azure ou usando um arquivo de configuração de rede no portal clássico. Você não pode criar ou modificar uma rede virtual clássica por meio do modelo de implantação Azure Resource Manager ou do portal do Azure. Para obter mais informações sobre roteamento de trânsito para redes virtuais clássicas, consulte o [blog do desenvolvedor da Microsoft](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Configurar o roteamento de trânsito em uma conexão site a site

Para configurar o roteamento de trânsito entre sua rede local e uma rede virtual com uma conexão site a site, você deve habilitar o BGP em todas as conexões intermediárias site a site usando o modelo de implantação do Gerenciador de recursos e o PowerShell. Consulte [como configurar o BGP em gateways de VPN do Azure usando o PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) para obter instruções.

O tráfego de trânsito por meio de gateways de VPN do Azure é possível por meio do modelo de implantação clássico, mas isso depende de espaços de endereço estaticamente definidos no arquivo de configuração de rede. O BGP ainda não tem suporte com redes virtuais do Azure e gateways de VPN por meio do modelo de implantação clássico. Sem o BGP, definir manualmente espaços de endereço de trânsito é propenso a erros e não é recomendável.

> [!Note]
> Configure conexões site a site clássicas usando o portal clássico do Azure ou usando um arquivo de configuração de rede no portal clássico. Você não pode criar ou modificar uma rede virtual clássica por meio do modelo de implantação Azure Resource Manager ou do portal do Azure. Para obter mais informações sobre roteamento de trânsito para redes virtuais clássicas, consulte o [blog do desenvolvedor da Microsoft](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Configurar o BGP para um gateway de VPN

O BGP é o protocolo de roteamento padrão usado na Internet para trocar informações de roteamento e acessibilidade entre duas ou mais redes. Quando o BGP é usado no contexto de redes virtuais do Azure, ele habilita os gateways de VPN do Azure e seus dispositivos VPN locais, conhecidos como vizinhos ou pares de BGP. Eles trocam "rotas" que informarão os dois gateways sobre a disponibilidade e a acessibilidade para que esses prefixos passem pelos gateways ou roteadores envolvidos. 

O BGP também pode habilitar o roteamento de trânsito entre várias redes propagando rotas que um gateway de BGP aprende de um par de BGP para todos os outros pares de BGP. Para obter mais informações, consulte [visão geral do BGP com o gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="configure-bgp-for-a-vpn-connection"></a>Configurar o BGP para uma conexão VPN

Para configurar uma conexão VPN que usa o BGP, consulte [como configurar o BGP em gateways de VPN do Azure usando o PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

Habilite o BGP no gateway de rede virtual criando um número de sistema autônomo (AS) para ele. Os gateways básicos não dão suporte a BGP. Para verificar a SKU do gateway, vá para a seção **visão geral** da folha **Gateway de VPN** no portal do Azure. Se sua SKU for **básica**, você precisará alterar a SKU (consulte [redimensionando o gateway](/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway)) para **VpnGw1**. 

A verificação da SKU causará 20 a 30 minutos de tempo de inatividade. Assim que o gateway tiver a SKU correta, você poderá adicionar o número de as usando o commandlet do PowerShell [set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) . Depois de configurar o número as, um IP de par de BGP para o gateway será fornecido automaticamente.

Você deve fornecer manualmente `LocalNetworkGateway` com um número de as e um endereço de par de BGP. Você pode definir os `ASN` `-BgpPeeringAddress` valores e usando o commandlet do PowerShell [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/new-azurermlocalnetworkgateway) ou [set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/set-azurermlocalnetworkgateway) . Alguns números AS são reservados para o Azure e você não pode usá-los conforme descrito em [sobre o BGP com o gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

O objeto de conexão deve ter o BGP habilitado. Você pode definir o `-EnableBGP` valor para `$True` por meio de [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection) ou [set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection).

### <a name="validate-the-bgp-configuration"></a>Validar a configuração de BGP

Para verificar se o BGP está configurado corretamente, você pode executar o `get-AzureRmVirtualNetworkGateway` e o `get-AzureRmLocalNetworkGateway` commandlets. Em seguida, você notará a saída relacionada ao BGP na `BgpSettingsText` parte. Por exemplo:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Criar uma conexão VPN ativa/ativa altamente disponível

As principais diferenças entre os gateways ativo/ativo e ativo/em espera são:

* Você deve criar duas configurações de IP de gateway com dois endereços IP públicos.
* Você deve definir o sinalizador **EnableActiveActiveFeature** .
* A SKU do gateway deve ser **VpnGw1**, **VpnGw2** ou **VpnGw3**.

Para obter alta disponibilidade para conectividade entre locais e de rede para rede, você deve implantar vários gateways de VPN e estabelecer várias conexões paralelas entre suas redes e o Azure. Para obter uma visão geral das opções de conectividade e topologia, consulte [conectividade altamente disponível entre locais e de rede para rede](../vpn-gateway/vpn-gateway-highlyavailable.md).

Para criar conexões ativas/ativas entre locais e de rede para rede, siga as instruções em [Configurar conexões VPN S2S ativas/ativas com gateways de VPN do Azure](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) para configurar um gateway de VPN do Azure no modo ativo/ativo.

> [!Note]  
> * Ao adicionar endereços ao gateway de rede local para o modo ativo/ativo habilitado para BGP, *adicione somente os endereços/32 dos pares BGP*. Se você adicionar mais endereços, eles serão considerados rotas estáticas e terão precedência sobre rotas BGP.
> * Você deve usar BGP diferentes como números para suas redes locais que estão se conectando ao Azure. (Se eles forem os mesmos, você precisará alterar sua rede virtual como número se o dispositivo VPN local já usa o ASN para emparelhar com outros vizinhos de BGP.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Alterar um tipo de gateway de VPN do Azure após a implantação

Você não pode alterar um tipo de gateway de rede virtual do Azure de baseado em política para baseado em rota ou de outra maneira diretamente. Primeiro, você deve excluir o gateway. Depois disso, o endereço IP e a chave pré-compartilhada não serão preservados. Em seguida, você pode criar um novo gateway do tipo desejado. 

Para excluir e criar um gateway, siga estas etapas:

1. Exclua todas as conexões associadas ao gateway original.
2. Exclua o gateway usando o portal do Azure, o PowerShell ou o PowerShell clássico: 
   * [Excluir um gateway de rede virtual usando o portal do Azure](../vpn-gateway/vpn-gateway-delete-vnet-gateway-portal.md)
   * [Excluir um gateway de rede virtual usando o PowerShell](../vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Excluir um gateway de rede virtual usando o PowerShell (clássico)](../vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell.md)
3. Siga as etapas em [criar o gateway de VPN](../vpn-gateway/tutorial-site-to-site-portal.md#VNetGateway) para criar o novo gateway do tipo desejado e conclua a configuração de VPN.

> [!Note]
> Esse processo levará cerca de 60 minutos.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conectividade entre VMs do Azure](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)

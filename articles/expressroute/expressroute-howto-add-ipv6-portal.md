---
title: 'Azure ExpressRoute: adicionar suporte a IPv6 usando o portal do Azure'
description: Saiba como adicionar suporte a IPv6 para se conectar a implantações do Azure usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 2/9/2021
ms.author: duau
ms.openlocfilehash: ef6ea9017a9aaa98e153df0d67f0b54fe5a2b64d
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124152"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Adicionar suporte a IPv6 para emparelhamento privado usando o portal do Azure (versão prévia)

Este artigo descreve como adicionar suporte a IPv6 para se conectar via ExpressRoute a seus recursos no Azure usando o portal do Azure. 

> [!Note]
> Este recurso está disponível atualmente para visualização em [regiões do Azure com zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). O circuito do ExpressRoute, portanto, pode ser criado usando qualquer local de emparelhamento, mas as implantações baseadas em IPv6 que se conectam devem estar em uma região com Zonas de Disponibilidade.

## <a name="register-for-public-preview"></a>Registrar-se para visualização pública
Antes de adicionar o suporte a IPv6, você deve primeiro registrar sua assinatura. Para se registrar, faça o seguinte por meio de Azure PowerShell:
1.  Entre no Azure e selecione a assinatura. Você deve fazer isso para a assinatura que contém o circuito do ExpressRoute, bem como a assinatura que contém suas implantações do Azure (se forem diferentes).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registre sua assinatura para visualização pública usando o seguinte comando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Sua solicitação será então aprovada pela equipe do ExpressRoute dentro de 2-3 dias úteis.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Em um navegador, vá para a [portal do Azure](https://portal.azure.com)e, em seguida, entre com sua conta do Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Adicionar emparelhamento privado IPv6 ao circuito do ExpressRoute

1. [Crie um circuito do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) ou navegue até o circuito existente que você deseja alterar.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Navegue até o circuito":::

2. Selecione a configuração de emparelhamento **privado do Azure** .

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Navegue até o emparelhamento":::

3. Adicione um emparelhamento privado IPv6 à sua configuração de emparelhamento privado IPv4 existente selecionando "ambas" para **sub-redes** ou apenas habilita o emparelhamento privado IPv6 no novo circuito selecionando "IPv6". Forneça um par de/126 sub-redes IPv6 que você possui para o link principal e links secundários. De cada uma dessas sub-redes, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o roteador dela. **Salve** a configuração de emparelhamento depois de especificar todos os parâmetros.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Adicionar emparelhamento privado IPv6":::

4. Depois que a configuração for aceita com êxito, você verá algo semelhante ao exemplo a seguir.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Exibir o emparelhamento privado IPv6":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Atualizar sua conexão com uma rede virtual existente

Siga as etapas abaixo se você tiver um ambiente existente de recursos do Azure em uma região com Zonas de Disponibilidade para o qual você gostaria de usar o emparelhamento privado IPv6 com.

1. Navegue até a rede virtual à qual seu circuito do ExpressRoute está conectado.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Navegue até a vnet":::

2. Navegue até a guia **espaço de endereço** e adicione um espaço de endereço IPv6 à sua rede virtual. **Salve** o espaço de endereço.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Adicionar espaço de endereço IPv6":::

3. Navegue até a guia **sub-redes** e selecione o **GatewaySubnet**. Marque **adicionar espaço de endereço IPv6** e forneça um espaço de endereço IPv6 para sua sub-rede. A sub-rede IPv6 do gateway deve ser/64 ou maior. **Salve** sua configuração depois de especificar todos os parâmetros.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Adicionar espaço de endereço IPv6 à sub-rede":::

4. Se você tiver um gateway com redundância de zona existente, navegue até o gateway de ExpressRoute e atualize o recurso para habilitar a conectividade IPv6. Caso contrário, [crie o gateway de rede virtual](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager) usando um SKU com redundância de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se você planeja usar o FastPath, use ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Atualizar o gateway":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Criar uma conexão com uma nova rede virtual

Siga as etapas abaixo se você planeja se conectar a um novo conjunto de recursos do Azure em uma região com Zonas de Disponibilidade usando o emparelhamento privado IPv6.

1. Crie uma rede virtual de pilha dupla com espaço de endereço IPv4 e IPv6. Para obter mais informações, consulte [criar uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network).

2. [Crie a sub-rede de gateway de pilha dupla](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-gateway-subnet).

3. [Crie o gateway de rede virtual](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager#create-the-virtual-network-gateway) usando um SKU com redundância de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se você planeja usar o FastPath, use ErGw3AZ (Observe que isso só está disponível para circuitos que usam o ExpressRoute Direct).

4. [Vincule sua rede virtual ao circuito do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

## <a name="limitations"></a>Limitações
Embora o suporte a IPv6 esteja disponível para conexões com implantações em regiões com Zonas de Disponibilidade, ele não oferece suporte aos seguintes casos de uso:

* Conexões com implantações no Azure por meio de um SKU de gateway de ExpressRoute não AZ
* Conexões com implantações em regiões não AZ
* Conexões Alcance Global entre circuitos do ExpressRoute
* Uso do ExpressRoute com WAN virtual
* FastPath com circuitos diretos não ExpressRoute
* Coexistência com o gateway de VPN

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas do ExpressRoute, consulte os seguintes artigos:

* [Verificando a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Solucionando problemas de desempenho de rede](expressroute-troubleshooting-network-performance.md)

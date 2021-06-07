---
title: 'Azure ExpressRoute: adicionar suporte a IPv6 usando o portal do Azure'
description: Saiba como adicionar suporte a IPv6 para se conectar a implantações do Azure usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561654"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Adicionar suporte a IPv6 para emparelhamento privado usando o portal do Azure (versão prévia)

Este artigo descreve como adicionar suporte a IPv6 para se conectar via ExpressRoute a seus recursos no Azure usando o portal do Azure. 

> [!Note]
> Este recurso está disponível atualmente para visualização em [regiões do Azure com zonas de disponibilidade](../availability-zones/az-region.md#azure-regions-with-availability-zones). O circuito do ExpressRoute, portanto, pode ser criado usando qualquer local de emparelhamento, mas as implantações baseadas em IPv6 que se conectam devem estar em uma região com Zonas de Disponibilidade.

## <a name="register-for-public-preview"></a>Registrar-se para visualização pública
Antes de adicionar o suporte a IPv6, você deve primeiro registrar sua assinatura. Para registrar, execute os seguintes comandos por meio do Azure PowerShell:

1.  Entre no Azure e selecione a assinatura. Execute estes comandos para a assinatura que contém o circuito do ExpressRoute e a assinatura que contém as implantações do Azure (se forem diferentes).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Registre sua assinatura para visualização pública usando o seguinte comando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Sua solicitação será então aprovada pela equipe do ExpressRoute dentro de 2-3 dias úteis.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Em um navegador, vá para a [portal do Azure](https://portal.azure.com)e, em seguida, entre com sua conta do Azure.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Adicionar emparelhamento privado IPv6 ao circuito do ExpressRoute

1. [Crie um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) ou navegue até o circuito existente que você deseja alterar.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Captura de tela da lista de circuitos do ExpressRoute.":::

1. Selecione a configuração de emparelhamento **privado do Azure** .

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Captura de tela da página Visão geral do ExpressRoute.":::

1. Adicione um emparelhamento privado IPv6 à sua configuração de emparelhamento privado IPv4 existente selecionando "ambas" para **sub-redes** ou apenas habilita o emparelhamento privado IPv6 no novo circuito selecionando "IPv6". Forneça um par de/126 sub-redes IPv6 que você possui para o link principal e links secundários. Em cada uma dessas sub-redes, você atribuirá o primeiro endereço IP utilizável ao seu roteador, uma vez que a Microsoft usa o segundo IP utilizável para seu roteador. **Salve** a configuração de emparelhamento depois de especificar todos os parâmetros.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Captura de tela da adição de IPv6 na página de emparelhamento privado.":::

1. Depois que a configuração for aceita com êxito, você verá algo semelhante ao exemplo a seguir.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Captura de tela do IPv6 configurado para emparelhamento privado.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Atualizar sua conexão com uma rede virtual existente

Siga as etapas abaixo se você tiver um ambiente existente de recursos do Azure em uma região com Zonas de Disponibilidade para o qual você gostaria de usar o emparelhamento privado IPv6 com.

1. Navegue até a rede virtual à qual seu circuito do ExpressRoute está conectado.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Captura de tela da lista de redes virtuais.":::

1. Navegue até a guia **espaço de endereço** e adicione um espaço de endereço IPv6 à sua rede virtual. **Salve** o espaço de endereço.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Captura de tela de adicionar espaço de endereço IPv6 à rede virtual.":::

1. Navegue até a guia **sub-redes** e selecione o **GatewaySubnet**. Marque **adicionar espaço de endereço IPv6** e forneça um espaço de endereço IPv6 para sua sub-rede. A sub-rede IPv6 do gateway deve ser/64 ou maior. **Salve** sua configuração depois de especificar todos os parâmetros.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Captura de tela de adicionar espaço de endereço IPv6 à sub-rede.":::

1. Se você tiver um gateway com redundância de zona existente, navegue até o gateway de ExpressRoute e atualize o recurso para habilitar a conectividade IPv6. Caso contrário, [crie o gateway de rede virtual](expressroute-howto-add-gateway-portal-resource-manager.md) usando um SKU com redundância de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se você planeja usar o FastPath, use ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Captura de tela da atualização do gateway.":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Criar uma conexão com uma nova rede virtual

Siga as etapas abaixo se você planeja se conectar a um novo conjunto de recursos do Azure em uma região com Zonas de Disponibilidade usando o emparelhamento privado IPv6.

1. Crie uma rede virtual de pilha dupla com espaço de endereço IPv4 e IPv6. Para obter mais informações, consulte [criar uma rede virtual](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Crie a sub-rede de gateway de pilha dupla](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Crie o gateway de rede virtual](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) usando um SKU com redundância de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se você planeja usar FastPath, use ErGw3AZ (Observe que essa opção só está disponível para circuitos que usam o ExpressRoute Direct).

1. [Vincule sua rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>Limitações
Embora o suporte a IPv6 esteja disponível para conexões com implantações em regiões com Zonas de Disponibilidade, ele não dá suporte aos seguintes casos de uso:

* Conexões com implantações no Azure por meio de um SKU de gateway de ExpressRoute não AZ
* Conexões com implantações em regiões não AZ
* Conexões Alcance Global entre circuitos do ExpressRoute
* Uso do ExpressRoute com WAN virtual
* FastPath com circuitos diretos não ExpressRoute
* FastPath com circuitos nos seguintes locais de emparelhamento: Dubai
* Coexistência com o gateway de VPN

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas do ExpressRoute, consulte os seguintes artigos:

* [Verificando a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Solucionando problemas de desempenho de rede](expressroute-troubleshooting-network-performance.md)
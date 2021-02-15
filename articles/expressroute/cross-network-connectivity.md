---
title: Conectividade entre redes do Azure
description: Esta página descreve um cenário de aplicativo para conectividade entre redes e solução com base em recursos de rede do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: 018afa1b2a31ebd44925a3fc79cbdc729b2d4695
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202490"
---
# <a name="cross-network-connectivity"></a>Conectividade entre redes

A Fabrikam Inc. tem uma grande presença física e implantação do Azure no leste dos EUA. A Fabrikam tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute. Da mesma forma, a contoso Ltd. tem uma presença e uma implantação do Azure no oeste dos EUA. A Contoso tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute.  

A Fabrikam Inc. adquire a contoso Ltd. Após a fusão, a Fabrikam deseja interconectar as redes. A figura a seguir ilustra o cenário:

![O cenário do aplicativo](./media/cross-network-connectivity/premergerscenario.png)

As setas tracejadas no meio da figura acima indicam as interconexões de rede desejadas. Especificamente, há três tipos de conexões cruzadas desejadas: 1) Fabrikam e contoso VNets Cross connect, 2) entre regiões regionais locais e VNets cruzadas (ou seja, conectando a rede local da Fabrikam à VNet contoso e conectando a rede local da Contoso à Fabrikam VNet) e 3) a Fabrikam e a contoso local rede de conexão cruzada. 

A tabela a seguir mostra a tabela de rotas do emparelhamento privado do ExpressRoute da Contoso Ltd., antes da fusão.

![Tabela de rotas do contoso ExpressRoute antes da fusão](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

A tabela a seguir mostra as rotas efetivas de uma VM na assinatura do contoso, antes da fusão. De acordo com a tabela, a VM na VNet está ciente do espaço de endereço da VNet e da rede local da Contoso, além dos padrões.

![Rotas de VM contoso antes da fusão](./media/cross-network-connectivity/contosovm-routes-premerger.png)

A tabela a seguir mostra a tabela de rotas do emparelhamento privado do ExpressRoute da Fabrikam Inc., antes da fusão.

![Tabela de rotas da Fabrikam ExpressRoute antes da fusão](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

A tabela a seguir mostra as rotas efetivas de uma VM na assinatura da Fabrikam, antes da fusão. De acordo com a tabela, a VM na VNet está ciente do espaço de endereço da VNet e da rede local da Fabrikam, além dos padrões.

![Rotas de VM da Fabrikam antes da fusão](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

Neste artigo, vamos percorrer passo a passo e discutir como obter as conexões cruzadas desejadas usando os seguintes recursos de rede do Azure:

* [Emparelhamento de rede virtual][Virtual network peering] 
* [Conexão de ExpressRoute de rede virtual][connection]
* [Alcance Global][Global Reach] 

## <a name="cross-connecting-vnets"></a>Conexão cruzada VNets

O emparelhamento de rede virtual (emparelhamento VNet) fornece o mais adequado e o melhor desempenho de rede ao conectar duas redes virtuais. O emparelhamento VNet dá suporte ao emparelhamento de dois VNets na mesma região do Azure (normalmente chamada de emparelhamento VNet) e em duas regiões do Azure diferentes (normalmente chamadas de emparelhamento de VNet global). 

Vamos configurar o emparelhamento de VNet global entre o VNets nas assinaturas contoso e Fabrikam Azure. Para saber como criar o emparelhamento de rede virtual entre duas redes virtuais, consulte [criar um artigo de emparelhamento de rede virtual][Configure VNet peering] .

A imagem a seguir mostra a arquitetura de rede depois de configurar o emparelhamento de VNet global.

![A arquitetura após o emparelhamento de VNet](./media/cross-network-connectivity/vnet-peering.png )

A tabela a seguir mostra as rotas conhecidas para a VM de assinatura contoso. Preste atenção à última entrada da tabela. Essa entrada é o resultado da conexão cruzada das redes virtuais.

![Rotas de VM contoso após o emparelhamento VNet](./media/cross-network-connectivity/contosovm-routes-peering.png)

A tabela a seguir mostra as rotas conhecidas para a VM de assinatura da Fabrikam. Preste atenção à última entrada da tabela. Essa entrada é o resultado da conexão cruzada das redes virtuais.

![Rotas de VM da Fabrikam após o emparelhamento VNet](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

O emparelhamento VNet vincula duas redes virtuais diretamente (veja que não há um próximo salto para a entrada *VNetGlobalPeering* nas duas tabelas acima)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Conexão cruzada VNets com as redes locais

Podemos conectar um circuito do ExpressRoute a várias redes virtuais. Consulte [limites de serviço e assinatura][Subscription limits] para o número máximo de redes virtuais que podem ser conectadas a um circuito de ExpressRoute. 

Vamos conectar o circuito do Fabrikam ExpressRoute à VNet de assinatura contoso e o circuito de ExpressRoute da Contoso para a VNet de assinatura da Fabrikam para habilitar a conectividade cruzada entre redes virtuais e redes locais. Para conectar uma rede virtual a um circuito do ExpressRoute em uma assinatura diferente, precisamos criar e usar uma autorização.  Consulte o artigo: [conectar uma rede virtual a um circuito do ExpressRoute][Connect-ER-VNet].

A imagem a seguir mostra a arquitetura de rede depois de configurar a conectividade cruzada do ExpressRoute para as redes virtuais.

![A arquitetura após a conexão cruzada do Expressroute ao qual](./media/cross-network-connectivity/exr-x-connect.png)

A tabela a seguir mostra a tabela de rotas do emparelhamento privado do ExpressRoute da Contoso Ltd., depois de conectar as redes virtuais às redes locais por meio do ExpressRoute. Veja que a tabela de rotas tem rotas que pertencem a ambas as redes virtuais.

![Tabela de rotas do contoso ExpressRoute após a conexão cruzada ExR e VNets](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

A tabela a seguir mostra a tabela de rotas do emparelhamento privado do ExpressRoute da Fabrikam Inc., depois de conectar as redes virtuais às redes locais por meio do ExpressRoute. Veja que a tabela de rotas tem rotas que pertencem a ambas as redes virtuais.

![Tabela de rotas da Fabrikam ExpressRoute após a conexão cruzada ExR e VNets](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

A tabela a seguir mostra as rotas conhecidas para a VM de assinatura contoso. Preste atenção às entradas de *Gateway de rede virtual* da tabela. A VM vê as rotas para as redes locais.

![Rotas de VM contoso após a conexão cruzada ExR e VNets](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

A tabela a seguir mostra as rotas conhecidas para a VM de assinatura da Fabrikam. Preste atenção às entradas de *Gateway de rede virtual* da tabela. A VM vê as rotas para as redes locais.

![Rotas de VM da Fabrikam após a conexão cruzada ExR e VNets](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>Nas assinaturas Fabrikam e/ou contoso, você também pode ter o spoke VNets na VNet do Hub respectivo (um design de Hub e spoke não é ilustrado nos diagramas de arquitetura neste artigo). As conexões cruzadas entre os gateways de VNet de Hub para o ExpressRoute também permitirão a comunicação entre hubs leste e oeste e spokes.
>

## <a name="cross-connecting-on-premises-networks"></a>Conexão cruzada de redes locais

O ExpressRoute Alcance Global fornece conectividade entre redes locais que estão conectadas a circuitos de ExpressRoute diferentes. Vamos configurar Alcance Global entre os circuitos da Contoso e da Fabrikam ExpressRoute. Como os circuitos do ExpressRoute estão em assinaturas diferentes, precisamos criar e usar uma autorização. Consulte o artigo [Configurar o ExpressRoute alcance global][Configure Global Reach] para obter orientações passo a passo.

A imagem a seguir mostra a arquitetura de rede depois de configurar Alcance Global.

![A arquitetura após a configuração de Alcance Global](./media/cross-network-connectivity/globalreach.png)

A tabela a seguir mostra a tabela de rotas do emparelhamento privado do ExpressRoute da Contoso Ltd. depois de configurar Alcance Global. Veja que a tabela de rotas tem rotas que pertencem a ambas as redes locais. 

![Tabela de rotas do contoso ExpressRoute após Alcance Global](./media/cross-network-connectivity/contosoexr-rt-gr.png)

A tabela a seguir mostra a tabela de rotas do emparelhamento privado do ExpressRoute da Fabrikam Inc., depois de configurar Alcance Global. Veja que a tabela de rotas tem rotas que pertencem a ambas as redes locais.

![Tabela de rotas da Fabrikam ExpressRoute após Alcance Global]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Próximas etapas

Consulte [perguntas frequentes sobre rede virtual][VNet-FAQ], para obter mais perguntas sobre vnet e emparelhamento de vnet. Consulte [perguntas frequentes sobre o expressroute][ER-FAQ] para obter mais perguntas sobre o expressroute e a conectividade de rede virtual.

Alcance Global é distribuído em um país/região por país/região. Para ver se Alcance Global está disponível nos países/regiões que você deseja, consulte [alcance global do ExpressRoute][Global Reach].

<!--Link References-->
[Virtual network peering]: ../virtual-network/virtual-network-peering-overview.md
[connection]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[Global Reach]: ./expressroute-global-reach.md
[Configure VNet peering]: ../virtual-network/create-peering-different-subscriptions.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[Subscription limits]: ../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits
[Connect-ER-VNet]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[ER-FAQ]: ./expressroute-faqs.md
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq
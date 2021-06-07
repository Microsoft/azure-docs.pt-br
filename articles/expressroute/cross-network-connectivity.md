---
title: Conectividade entre redes do Microsoft Azure
description: Esta página descreve um cenário de aplicativo para conectividade entre redes e solução com base em recursos de rede do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: 018afa1b2a31ebd44925a3fc79cbdc729b2d4695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202490"
---
# <a name="cross-network-connectivity"></a>Conectividade entre redes

A Fabrikam Inc. tem uma grande presença física e implantação do Azure no leste dos EUA. A Fabrikam tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute. Similarmente, a Contoso Ltd. tem uma presença e implantação do Azure na região Oeste dos EUA. A Contoso tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute.  

A Fabrikam Inc. adquire a Contoso Ltd. Após a fusão, a Fabrikam deseja interconectar as redes. A figura a seguir ilustra o cenário:

![O cenário do aplicativo](./media/cross-network-connectivity/premergerscenario.png)

As setas tracejadas no meio da figura acima indicam as interconexões de rede desejadas. Especificamente, há três tipos de conexões cruzadas desejadas: 1) a conexão entre a Fabrikam e as VNets da Contoso, 2) a conexão entre locais regionais e as VNets (ou seja, conectada à rede local da Fabrikam à VNet da Contoso e conectada à rede local da Contoso à VNet da Fabrikam) e 3) a conexão das redes locais da Fabrikam e da Contoso. 

A tabela a seguir mostra a tabela de rotas de emparelhamento privado do ExpressRoute da Contoso Ltd. antes da fusão.

![Tabela de rotas do ExpressRoute da Contoso antes da fusão](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

A tabela a seguir mostra as rotas efetivas de uma VM na assinatura da Contoso, antes da fusão. De acordo com a tabela, a VM na VNet está ciente do espaço de endereço da VNet e da rede local da Contoso, além das redes padrões.

![Rotas de VM da Contoso antes da fusão](./media/cross-network-connectivity/contosovm-routes-premerger.png)

A tabela a seguir mostra a tabela de rotas de emparelhamento privado do ExpressRoute da Fabrikam Inc. antes da fusão.

![Tabela de rotas do ExpressRoute da Fabrikam antes da fusão](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

A tabela a seguir mostra as rotas efetivas de uma VM na assinatura da Fabrikam, antes da fusão. De acordo com a tabela, a VM na VNet está ciente do espaço de endereço da VNet e da rede local da Fabrikam, além dos padrões.

![Rotas de VM da Fabrikam antes da fusão](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

Neste artigo, você verá o passo a passo e discutiremos como obter as conexões cruzadas desejadas usando os recursos de rede do Azure a seguir:

* [Emparelhamento de rede virtual][Virtual network peering] 
* [Conexão de rede virtual do ExpressRoute][connection]
* [Alcance Global][Global Reach] 

## <a name="cross-connecting-vnets"></a>Conexão cruzada de VNets

O emparelhamento de rede virtual (emparelhamento VNet) fornece o melhor desempenho de rede e mais adequado ao conectar duas redes virtuais. O emparelhamento VNet dá suporte ao emparelhamento de dois VNets na mesma região do Azure (normalmente chamada de emparelhamento VNet) e em duas regiões diferentes do Azure (normalmente chamadas de emparelhamento global de VNet). 

Vamos configurar o emparelhamento Global de VNet entre os VNets nas assinaturas Azure da Contoso e da Fabrikam. Para saber mais sobre como criar o emparelhamento de rede virtual entre duas redes virtuais, consulte o artigo [Criar um emparelhamento de rede virtual][Configure VNet peering].

A imagem a seguir mostra a arquitetura de rede depois de configurar o emparelhamento global de VNet.

![A arquitetura após o emparelhamento de VNet](./media/cross-network-connectivity/vnet-peering.png )

A tabela a seguir mostra as rotas conhecidas para a VM de assinatura da Contoso. Observe a última entrada da tabela. Essa entrada é o resultado da conexão cruzada das redes virtuais.

![Rotas de VM da Contoso após o emparelhamento de VNet](./media/cross-network-connectivity/contosovm-routes-peering.png)

A tabela a seguir mostra as rotas conhecidas para a VM de assinatura da Fabrikam. Observe a última entrada da tabela. Essa entrada é o resultado da conexão cruzada das redes virtuais.

![Rotas de VM da Fabrikam após o emparelhamento de VNet](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

O emparelhamento VNet vincula duas redes virtuais diretamente (observe que não há um salto a seguir para a entrada *VNetGlobalPeering* nas duas tabelas acima)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Conexão cruzada de VNets com as redes locais

Podemos conectar um circuito de ExpressRoute a várias redes virtuais. Consulte [Limites de serviço e assinatura][Subscription limits] para saber o número máximo de redes virtuais que podem ser conectadas a um circuito de ExpressRoute. 

Vamos conectar o circuito de ExpressRoute da Fabrikam à VNet de assinatura da Contoso assim como o circuito de ExpressRoute da Contoso à VNet de assinatura da Fabrikam para habilitar a conectividade cruzada entre redes virtuais e redes locais. Para conectar uma rede virtual a um circuito de ExpressRoute em uma assinatura diferente, precisamos criar e usar uma autorização.  Confira o artigo: [Conectar uma rede virtual a um circuito de ExpressRoute][Connect-ER-VNet].

A imagem a seguir mostra a arquitetura de rede depois de configurar a conectividade cruzada do ExpressRoute para as redes virtuais.

![A arquitetura após a conexão cruzada do ExpressRoutes](./media/cross-network-connectivity/exr-x-connect.png)

A tabela a seguir mostra a tabela de rotas do emparelhamento privado do ExpressRoute da Contoso Ltd. depois de conectar as redes virtuais às redes locais por meio do ExpressRoute. Observe que essa tabela tem rotas que pertencem as duas redes virtuais.

![Tabela de rotas de ExpressRoute da Contoso após a conexão cruzada de ExR e VNets](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

A tabela a seguir mostra a tabela de rotas do emparelhamento privado do ExpressRoute da Fabrikam Inc. depois de conectar as redes virtuais às redes locais por meio do ExpressRoute. Observe que essa tabela tem rotas que pertencem as duas redes virtuais.

![Tabela de rotas de ExpressRoute da Fabrikam após a conexão cruzada de ExR e VNets](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

A tabela a seguir mostra as rotas conhecidas para a VM de assinatura da Contoso. Observe as entradas da tabela *Gateway de rede virtual*. A VM prevê rotas para as duas redes locais.

![Rotas de VM da Contoso após a conexão cruzada de ExR e VNets](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

A tabela a seguir mostra as rotas conhecidas para a VM de assinatura da Fabrikam. Observe as entradas da tabela *Gateway de rede virtual*. A VM prevê rotas para as duas redes locais.

![Rotas de VM da Fabrikam após a conexão cruzada de ExR e VNets](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>Nas assinaturas da Fabrikam e/ou da Contoso, você também pode ter VNets de spoke no respectivo VNet do hub (um design de hub e spoke não é ilustrado nos diagramas de arquitetura neste artigo). As conexões cruzadas entre os gateways de VNet de hub para o ExpressRoute também permitirão a comunicação entre hubs e spokes do leste e oeste.
>

## <a name="cross-connecting-on-premises-networks"></a>Conexão cruzada entre redes locais

O ExpressRoute de Alcance Global fornece conectividade entre redes locais conectadas a circuitos diferentes de ExpressRoute. Vamos configurar o Alcance Global entre os circuitos de ExpressRoute da Contoso e da Fabrikam. Como os circuitos de ExpressRoute estão em assinaturas diferentes, precisamos criar e usar uma autorização. Confira o artigo [Configurar Alcance Global do ExpressRoute][Configure Global Reach] para obter o passo a passo.

A imagem a seguir mostra a arquitetura de rede depois de configurar o Alcance Global.

![A arquitetura após a configuração de Alcance Global](./media/cross-network-connectivity/globalreach.png)

A tabela a seguir mostra a tabela de rotas de emparelhamento privado do ExpressRoute da Contoso Ltd. após a configuração do Alcance Global. Observe que essa tabela tem rotas que pertencem as duas redes locais. 

![Tabela de rotas do ExpressRoute da Contoso após configuração de Alcance Global](./media/cross-network-connectivity/contosoexr-rt-gr.png)

A tabela a seguir mostra a tabela de rotas de emparelhamento privado do ExpressRoute da Fabrikam Ltd. após a configuração do Alcance Global. Observe que essa tabela tem rotas que pertencem as duas redes locais.

![Tabela de rotas do ExpressRoute da Fabrikam após configuração de Alcance Global]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Próximas etapas

Consulte [Perguntas frequentes sobre rede virtual][VNet-FAQ], para ver mais perguntas sobre Vnet e emparelhamento de Vnet. Consulte [Perguntas frequentes sobre o ExpressRoute][ER-FAQ] para ver mais perguntas sobre o ExpressRoute a conectividade de rede virtual.

O Alcance Global é distribuído em um país/região com base em um país/região. Para saber se o Alcance Global está disponível nos países/regiões desejados, confira [Alcance Global do ExpressRoute][Global Reach].

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
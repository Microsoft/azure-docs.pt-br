---
title: 'Azure ExpressRoute: conectar-se a Microsoft Cloud usando Alcance Global'
description: Saiba como o Azure ExpressRoute Alcance Global pode vincular circuitos do ExpressRoute juntos para fazer uma rede privada entre suas redes locais.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 50679e11697a4227af69b8568c5f3cd23fe26cbe
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662790"
---
# <a name="expressroute-global-reach"></a>Alcance Global do ExpressRoute
O ExpressRoute é uma maneira privada e resiliente de conectar suas redes locais ao Microsoft Cloud. Você pode acessar muitos serviços de nuvem da Microsoft, como o Azure e Microsoft 365 de seu data center particular ou sua rede corporativa. Por exemplo, é possível ter uma filial em São Francisco com um circuito ExpressRoute no Vale do Silício e outra filial em Londres com um circuito ExpressRoute na mesma cidade. Ambas as filiais têm conectividade de alta velocidade com os recursos do Azure no oeste dos EUA e Sul do Reino Unido. No entanto, as filiais não podem se conectar e enviar dados diretamente entre si. Em outras palavras, 10.0.1.0/24 pode enviar dados para a rede 10.0.3.0/24 e 10.0.4.0/24, mas não para a rede 10.0.2.0/24.

![Diagrama que mostra os circuitos não vinculados ao Alcance Global de rota expressa.][1]

Com o **Alcance Global do ExpressRoute**, é possível vincular circuitos do ExpressRoute em conjunto para criar uma rede privada entre as redes locais. No exemplo acima, com a adição do Alcance Global do ExpressRoute, a filial de São Francisco (10.0.1.0/24) pode trocar dados diretamente com a filial de Londres (10.0.2.0/24) por meio dos circuitos do ExpressRoute existentes e da rede global da Microsoft. 

![Diagrama que mostra os circuitos vinculados junto com Alcance Global de rota expressa.][2]

## <a name="use-case"></a>Caso de uso
O Alcance Global do ExpressRoute foi desenvolvido para complementar a implementação de WAN do provedor de serviços e conectar as filiais ao redor do mundo. Por exemplo, se o provedor de serviços opera principalmente nos Estados Unidos e vinculou todas as filiais nos EUA, mas o provedor de serviços não opera no Japão e em Hong Kong, com o Alcance Global do ExpressRoute é possível trabalhar com um provedor de serviços local e a Microsoft conectará as filiais àquelas dos EUA usando o ExpressRoute e nossa rede global.

![Diagrama que mostra um caso de uso para Alcance Global de rota expressa.][3]

## <a name="availability"></a>Disponibilidade 
O ExpressRoute Alcance Global tem suporte nos seguintes locais. 

> [!NOTE] 
> Para habilitar o ExpressRoute Alcance Global entre [regiões geopolítica diferentes](expressroute-locations-providers.md#locations), seus circuitos devem ser **SKU Premium**.

* Austrália
* Canadá
* França
* Alemanha
* RAE de Hong Kong
* Irlanda
* Japão
* Coreia do Sul
* Países Baixos
* Nova Zelândia
* Noruega
* Singapura
* África do Sul (somente Joanesburgo)
* Suécia
* Suíça
* Reino Unido
* Estados Unidos

## <a name="next-steps"></a>Próximas etapas
- Exiba o [alcance global perguntas frequentes](expressroute-faqs.md#globalreach).
- Saiba como [habilitar alcance global](expressroute-howto-set-global-reach.md).
- Saiba como [vincular um circuito do ExpressRoute à sua rede virtual](expressroute-howto-linkvnet-arm.md).

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagrama sem alcance global"
[2]: ./media/expressroute-global-reach/2.png "diagrama com alcance global"
[3]: ./media/expressroute-global-reach/3.png "caso de uso do alcance global"

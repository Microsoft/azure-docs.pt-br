---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: ccompy
ms.openlocfilehash: 0b93111357cf0d6e57eeb5495d50bd18a15dca77
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812770"
---
* Os sistemas multilocatários que oferecem suporte a toda a gama de planos de preços, exceto isolados.
* O Ambiente do Serviço de Aplicativo, que é implantado em sua VNet e dá suporte a aplicativos de plano de preços isolados.

O recurso de integração VNet é usado em aplicativos multilocatários. Se o aplicativo está no [Ambiente do Serviço de Aplicativo][ASEintro], ele já está em uma rede virtual e não requer o uso do recurso de Integração VNet para acessar recursos na mesma VNet. Para obter mais informações sobre todos os recursos de rede, consulte [recursos de rede do serviço de aplicativo][Networkingfeatures].

A integração VNet dá ao seu aplicativo acesso aos recursos em sua VNet, mas não concede acesso privado de entrada ao seu aplicativo por meio da VNet. Acesso ao site privado refere-se a tornar um aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. A integração VNet é usada apenas para fazer chamadas de saída do seu aplicativo para sua VNet. O recurso de integração VNet se comporta de maneira diferente quando é usado com VNet na mesma região e com VNet em outras regiões. O recurso integração VNet tem duas variações:

* **Integração de VNet regional**: quando você se conecta a redes virtuais Azure Resource Manager na mesma região, você deve ter uma sub-rede dedicada na VNet com a qual está se integrando.
* **Integração de vnet necessária ao gateway**: quando você se conecta à VNet em outras regiões ou a uma rede virtual clássica na mesma região, você precisa de um gateway de rede virtual do Azure provisionado na VNet de destino.

Os recursos de integração VNet:

* Exigir um plano de preços Standard, Premium, PremiumV2, PremiumV3 ou elástico Premium.
* Suporte a TCP e UDP.
* Trabalhe com aplicativos de serviço Azure App e aplicativos de funções.

Há algumas coisas para as quais a integração VNet não dá suporte, como:

* A montagem de uma unidade.
* Integração de Active Directory.
* Output.

Gateway-a integração VNet necessária fornece acesso a recursos somente na VNet de destino ou em redes conectadas à VNet de destino com emparelhamento ou VPNs. A integração VNet exigida pelo gateway não habilita o acesso aos recursos disponíveis nas conexões do Azure ExpressRoute ou trabalha com pontos de extremidade de serviço.

Independentemente da versão usada, a integração VNet dá ao seu aplicativo acesso aos recursos em sua VNet, mas não concede acesso privado de entrada ao seu aplicativo por meio da VNet. Acesso ao site privado refere-se a tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma VNet do Azure. A integração VNet é apenas para fazer chamadas de saída de seu aplicativo para sua VNet.

<!--Links-->
[ASEintro]: ../articles/app-service/environment/intro.md
[Networkingfeatures]: ../articles/app-service/networking-features.md

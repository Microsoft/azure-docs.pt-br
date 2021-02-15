---
title: Sobre a atualização da largura de banda do circuito | ExpressRoute do Azure
description: Neste artigo, conheça as práticas recomendadas para atualizar a largura de banda do circuito do ExpressRoute
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: duau
ms.openlocfilehash: 7831e7944321e074c312853e1534c47970ebdfdb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397943"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>Sobre a atualização da largura de banda do circuito do ExpressRoute

O ExpressRoute permite a conectividade dedicada e privada à rede global da Microsoft. A conectividade é facilitada pela rede de um parceiro do ExpressRoute ou por uma conexão direta com os dispositivos Microsoft Enterprise Edge (MSEE). Depois que a conectividade física tiver sido configurada e testada, você poderá habilitar a conectividade de camada 2 e camada 3 Criando um circuito do ExpressRoute e Configurando o emparelhamento.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Atualizar largura de banda do circuito

Para atualizar a largura de banda do circuito, o parceiro ExpressRoute Direct ou ExpressRoute precisa ter [largura de banda disponível suficiente](#considerations) para que a atualização tenha sucesso.

Se a capacidade estiver disponível, você poderá atualizar o circuito usando os seguintes métodos:

* [Azure portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [CLI do Azure](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Considerações sobre a capacidade

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Largura de banda do parceiro ExpressRoute insuficiente

Se o parceiro ExpressRoute não tiver capacidade suficiente, você precisará criar um novo circuito, configurado para a largura de banda desejada. Para manter a conectividade, não exclua o circuito antigo até que o circuito recém-criado seja provisionado, o emparelhamento tenha sido configurado e (em relação ao emparelhamento privado) o objeto de conexão com o gateway de rede virtual ExpressRoute foi provisionado.

Se o seu parceiro do ExpressRoute não tiver capacidade suficiente disponível, você precisará solicitar capacidade adicional no local de emparelhamento desejado. Depois que a nova capacidade for provisionada, você poderá usar as etapas contidas nos artigos da seção [Atualizar largura de banda do circuito](#upgrade) para criar um novo circuito, configurar a conectividade e excluir o circuito antigo.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Largura de banda direta do ExpressRoute insuficiente

Se o ExpressRoute Direct não tiver capacidade suficiente, você poderá excluir os circuitos associados ao recurso do ExpressRoute Direct que não são mais necessários ou criar um novo recurso direto do ExpressRoute. Para obter orientações sobre como gerenciar o recurso direto do ExpressRoute, consulte [como configurar o Expressroute Direct](how-to-expressroute-direct-portal.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar e modificar um circuito](expressroute-howto-circuit-portal-resource-manager.md)
* [Criar e modificar a configuração de emparelhamento](expressroute-howto-routing-portal-resource-manager.md)
* [Vincular uma rede virtual a um circuito de ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)

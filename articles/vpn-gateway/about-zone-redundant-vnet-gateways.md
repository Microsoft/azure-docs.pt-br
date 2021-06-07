---
title: Sobre os gateways de rede virtual com redundância de zona em Zonas de Disponibilidade do Azure
description: Implante gateways VPN e ExpressRoute no Zonas de Disponibilidade do Azure para proporcionar resiliência, escalabilidade e maior disponibilidade para gateways de rede virtual.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2030469262baf406635fd170af384e154fec6ae6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89401105"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Sobre os gateways de rede virtual com redundância de zona em Zonas de Disponibilidade do Azure

Você pode implantar gateways VPN e ExpressRoute no [zonas de disponibilidade do Azure](../availability-zones/az-overview.md). Isso traz resiliência, escalabilidade e maior disponibilidade para os gateways de rede virtual. A implantação de gateways em Zonas de Disponibilidade do Azure separa de forma física e lógica os gateways em uma região, enquanto protege a conectividade de rede local com o Azure contra falhas no nível da zona.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Gateways com redundância de zona

Para implantar automaticamente seus gateways de rede virtual entre zonas de disponibilidade, você pode usar gateways de rede virtual com redundância de zona. Com os gateways com redundância de zona, você pode aproveitar a resiliência de zona para acessar seus serviços escalonáveis e de missão crítica no Azure.

<br>
<br>

![gráfico de gateways com redundância de zona](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Gateways em zona

Para implantar gateways em uma zona específica, você pode usar gateways em zona. Ao implantar um gateway em zona, todas as instâncias do gateway são implantadas na mesma zona de disponibilidade.

<br>
<br>

![gráfico de gateways em zona](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>SKUs do Gateway

Os gateways zonais e com redundância de zona estão disponíveis como novas SKUs de gateway. Adicionamos novas SKUs de gateway de rede virtual em regiões do Azure AZ. Essas SKUs são semelhantes às SKUs existentes correspondentes para o Gateway de VPN e ExpressRoute, exceto que eles são específicos para gateways com redundância de zona e em zona. Você pode identificar essas SKUs pelo "AZ" no nome do SKU.

Para obter informações sobre SKUs de gateway, consulte [SKUs de gateway de VPN](vpn-gateway-about-vpngateways.md#gwsku) e SKUs de gateway de [ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>SKUs de IP público

Os gateways com redundância de zona e os gateways em zona contam com o SKU *Standard* de recurso de IP público do Azure. A configuração do recurso de IP público do Azure determina se o gateway implantado é do tipo em zona ou com redundância de zona. Se você criar um recurso de IP público com uma SKU *Básica*, o gateway não terá redundância de zona e os recursos dele serão regionais.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Gateways com redundância de zona

Quando você cria um endereço IP público usando a SKU de IP público **Standard** sem especificar uma zona, o comportamento é diferente dependendo se o gateway é um Gateway de VPN ou ExpressRoute. 

* Para um Gateway de VPN, as duas instâncias do gateway serão implantadas em quaisquer duas zonas entre essas três para fornecer a redundância de zona. 
* Para um gateway ExpressRoute, uma vez que pode haver mais de duas instâncias, o gateway poderá ser distribuído entre todas as três zonas.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Gateways em zona

Quando você cria um endereço IP público usando a SKU de IP público **Standard** e especifica a Zona (1, 2 ou 3), todas as instâncias de gateway são implantadas na mesma zona.

### <a name="regional-gateways"></a><a name="piprg"></a>Gateways regionais

Quando você cria um endereço IP público usando a SKU de IP público **Básica**, o gateway é implantado como um gateway regional e não tem nenhuma redundância de zona integrada no gateway.

## <a name="faq"></a><a name="faq"></a>Perguntas frequentes

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que mudará quando eu implantar essas novas SKUs?

Da sua perspectiva, você poderá implantar seus gateways com redundância de zona. Isso significa que todas as instâncias dos gateways serão implantadas em Zonas de Disponibilidade do Azure e cada Zona de Disponibilidade será um domínio de atualização e falha diferente. Isso torna seus gateways mais confiáveis, disponíveis e resilientes a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usar o portal do Azure?

Sim, você pode usar o portal do Azure para implantar as novas SKUs. No entanto, você verá essas novas SKUs somente em regiões do Azure com Zonas de Disponibilidade do Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Quais regiões estão disponíveis para eu usar as novas SKUs?

As novas SKUs estão disponíveis nas regiões do Azure que têm as regiões Zonas de Disponibilidade do Azure-EUA Central, França central, Europa Setentrional, Europa Ocidental e oeste dos EUA 2, leste dos EUA, leste dos Estados Unidos 2, Sudeste Asiático, leste do Japão, Sul do Reino Unido. No futuro, disponibilizaremos os gateways com redundância de zona em outras Regiões Públicas do Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso alterar/migrar/atualizar meus gateways de rede virtual existentes para gateways com redundância de zona ou em zona?

No momento, a migração de gateways de rede virtual existentes para gateways com redundância de zona ou em zona não é compatível. No entanto, você pode excluir o gateway existente e recriar um gateway com redundância de zona ou em zona.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Posso implantar gateways de VPN e ExpressRoute na mesma rede virtual?

A coexistência de gateways de VPN e do ExpressRoute na mesma rede virtual é compatível. No entanto, reserve um intervalo de endereços IP /27 para a sub-rede de gateway.

## <a name="next-steps"></a>Próximas etapas

[Criar um gateway de rede virtual com redundância de zona](create-zone-redundant-vnet-gateway.md)

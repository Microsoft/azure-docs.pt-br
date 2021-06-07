---
title: Rede virtual do Azure-conceitos e práticas recomendadas
description: Saiba mais sobre os conceitos e as práticas recomendadas da rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 0a9945a58aa6ec49ad58f3a0a0d03ea75e30f6d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223611"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Conceitos e práticas recomendadas da rede virtual do Azure

Este artigo descreve os principais conceitos e práticas recomendadas para a VNet (rede virtual) do Azure.

## <a name="vnet-concepts"></a>Conceitos de VNet

- **Espaço de endereço:** Ao criar uma VNet, é necessário especificar um espaço de endereço IP privado personalizado usando endereços públicos e privados (RFC 1918). O Azure atribui um endereço IP particular aos recursos em uma rede virtual do espaço de endereço que você atribuiu. Por exemplo, se você implantar uma VM em uma VNet com o espaço de endereço 10.0.0.0/16, a VM receberá um IP privado como 10.0.0.4.
- **Sub-redes:** As sub-redes permitem que você segmente a rede virtual em uma ou mais sub-redes e aloque uma parte do espaço de endereço da rede virtual a cada sub-rede. Em seguida, você pode implantar recursos do Azure em uma sub-rede específica. Assim como em uma rede tradicional, as sub-redes permitem que você divida o espaço de endereço da sua VNet em segmentos que são apropriados para a rede interna da organização. Isso também melhora a eficiência de alocação de endereço. É possível proteger os recursos dentro de sub-redes usando grupos de segurança de rede. Para saber mais, confira [Grupos de segurança de rede](./network-security-groups-overview.md).
- **Regiões**: Uma VNet tem como escopo uma única região/localização; no entanto, é possível conectar várias redes virtuais de regiões diferentes usando o Emparelhamento de Rede Virtual.
- **Assinatura:** A VNet está no escopo de uma assinatura. É possível implementar várias redes virtuais em cada [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [região](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) do Azure.

## <a name="best-practices"></a>Práticas recomendadas

Ao criar sua rede no Azure, é importante se lembrar dos seguintes princípios de design universais:

- Cuidado para não sobrepor espaços de endereço. Verifique se o espaço de endereço de sua VNet (bloco CIDR) não se sobrepõe aos outros intervalos de rede da sua organização.
- Suas sub-redes não devem abranger todo o espaço de endereço da VNet. Planeje com antecedência e reserve algum espaço de endereço para o futuro.
- Recomendamos que você tenha menos VNETs maiores do que várias VNETs pequenas. Isso evitará a sobrecarga no gerenciamento.
- Projeta suas VNets atribuindo NSGs (Grupos de Segurança de Rede) às sub-redes abaixo deles.

## <a name="next-steps"></a>Próximas etapas

 Para começar a usar uma rede virtual, crie uma, implante algumas VMs nela, e se comunique entre as VMs. Para saber como, confira o início rápido [Criar uma rede virtual](quick-create-portal.md).
---
title: Azure Load Balancer e Zonas de Disponibilidade
titleSuffix: Azure Load Balancer
description: Com este roteiro de aprendizagem, comece a usar o Azure Standard Load Balancer e Zonas de Disponibilidade.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699106"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer e Zonas de Disponibilidade

O Azure Load Balancer dá suporte a cenários de zonas de disponibilidade. Você pode usar Standard Load Balancer para aumentar a disponibilidade em todo o cenário alinhando recursos com o e a distribuição entre zonas.  Examine este documento para entender esses conceitos e diretrizes de design de cenário fundamental

Um Load Balancer pode ser com **redundância de zona,** zonas ou **não** zonal. Para configurar as propriedades relacionadas à zona (mencionadas acima) para o balanceador de carga, selecione o tipo apropriado de front-end necessário.

## <a name="zone-redundant"></a>Redundância de zona

Em uma região com Zonas de Disponibilidade, um Standard Load Balancer pode ser com redundância de zona. Esse tráfego é servido por um único endereço IP.

Um único endereço IP de front-end sobreviver A falhas de zona. O IP de front-end pode ser usado para alcançar todos os membros do pool de back-end (não impactados), independentemente da zona. Uma ou mais zonas de disponibilidade podem falhar e o caminho de dados sobreviver, desde que uma zona na região permaneça íntegra.

O endereço IP do front-end é servido simultaneamente por várias implantações de infraestrutura independentes em várias zonas de disponibilidade. Qualquer nova tentativa ou restabelecimento terá êxito em outras zonas não afetadas pela falha de zona.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="NAT de Rede Virtual">
</p>

*Figura: balanceador de carga com redundância de zona*

## <a name="zonal"></a>Zonal

Você pode optar por ter um front-end garantido para uma única zona, o que é conhecido *como zonal.*  Esse cenário significa que qualquer fluxo de entrada ou saída é servido por uma única zona em uma região.  O front-end compartilha o destino com a integridade da zona.  O caminho de dados não é afetado por falhas em outras zonas além de onde foi garantido. É possível utilizar front-ends zonais para expor um endereço IP por Zona de Disponibilidade.  

Além disso, há suporte para o uso de front-ends zonais diretamente para pontos de extremidade com balanceamento de carga em cada zona. Você pode usar essa configuração para expor pontos de extremidade com balanceamento de carga por zona para monitorar individualmente cada zona. Para pontos de extremidade públicos, você pode integrá-los a um produto de balanceamento de carga de DNS como o [Gerenciador de tráfego](../traffic-manager/traffic-manager-overview.md) e usar um único nome DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="NAT de Rede Virtual">
</p>

*Figura: balanceador de carga zonal*

Para um front-end público do Load Balancer, você adiciona um parâmetro de **zonas** ao IP público. Esse IP público é referenciado pela configuração de IP de front-end usada pela respectiva regra.

Para um front-end do balanceador de carga interno, adicione um parâmetro de **zonas** à configuração de IP de front-end do balanceador de carga interno. Um front-end zonal garante um endereço IP em uma sub-rede para uma zona específica.

## <a name="design-considerations"></a><a name="design"></a> Considerações de design

Agora que você entende as propriedades relacionadas à zona para Standard Load Balancer, as considerações de design a seguir podem ajudar à medida que você projeta para alta disponibilidade.

### <a name="tolerance-to-zone-failure"></a>Tolerância a falhas de zona

- Um Load Balancer com **redundância de zona** pode servir a um recurso zonal em qualquer região com um endereço IP.  O IP pode sobreviver a uma ou mais falhas de zona, contanto que pelo menos uma zona permaneça íntegra na região.
- Um front-end **zonal** é uma redução do serviço para uma única zona e compartilha destino com a respectiva zona. Se a zona em que a implantação está diminuindo, sua implantação não sobreviverá a essa falha.

É recomendável usar Load Balancer com redundância de zona para suas cargas de trabalho de produção.

### <a name="control-vs-data-plane-implications"></a>Controlar as implicações do plano de dados vs

A redundância de zona não implica em implica plano de dados ou plano de controle. Os fluxos com redundância de zona podem usar qualquer zona e seus fluxos usarão todas as zonas íntegras em uma região. Em uma falha de zona, os fluxos de tráfego usando zonas íntegras não são afetados.

Os fluxos de tráfego usando uma zona no momento da falha de zona podem ser afetados, mas os aplicativos podem ser recuperados. O tráfego continua nas zonas íntegras dentro da região após a retransmissão quando o Azure convergiu em volta da falha de zona.

Examine os [padrões de design de nuvem do Azure](/azure/architecture/patterns/) para melhorar a resiliência do seu aplicativo para cenários de falha.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [zonas de disponibilidade](../availability-zones/az-overview.md)
- Saiba mais sobre o [Standard Load Balancer](./load-balancer-overview.md)
- Saiba como [balancear a carga de VMs em uma zona usando um Standard Load Balancer](./quickstart-load-balancer-standard-public-cli.md) zonal
- Saiba como [balancear a carga de VMs entre zonas usando um Standard Load Balancer com redundância de zona](./quickstart-load-balancer-standard-public-cli.md)
- Saiba mais sobre os [padrões de design de nuvem do Azure](/azure/architecture/patterns/) para melhorar a resiliência do seu aplicativo para cenários de falha.

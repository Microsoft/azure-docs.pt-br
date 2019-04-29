---
title: Visão geral do IPv6 para o Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Entender o suporte a IPv6 para o Azure Load Balancer e VMs com balanceamento de carga.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: ipv6, azure load balancer, pilha dual, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: kumud
ms.openlocfilehash: 894a56c2e51e8fa8a2d72253563d218416ace4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861933"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Visão geral do IPv6 para o Azure Load Balancer


>[!NOTE] 
>O Azure Load Balancer é compatível com dois tipos diferentes: Básico e Standard. Este artigo discute o Load Balancer Basic. Para obter mais informações sobre o Load Balancer Standard, veja [Visão geral do Load Balancer Standard](load-balancer-standard-overview.md).

Balanceadores de carga voltados para a Internet podem ser implantados com um endereço IPv6. Além de conectividade IPv4, isso permite os seguintes recursos:

* Conectividade IPv6 nativa ponta a ponta entre clientes de Internet pública e VMs (máquinas virtuais) do Azure por meio do balanceador de carga.
* Saída IPv6 nativa ponta a ponta entre VMs e clientes habilitados para IPv6 da Internet pública.

A figura a seguir ilustra a funcionalidade do IPv6 para o Azure Load Balancer.

![Azure Load Balancer com IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Uma vez implantado, um cliente de internet habilitado para IPv4 ou IPv6 pode se comunicar com endereços IPv4 ou IPv6 públicos (ou nomes de host) do balanceador de carga voltado para a Internet do Azure. O balanceador de carga encaminha os pacotes IPv6 para os endereços IPv6 privados das VMs usando a conversão de endereços de rede (NAT). O cliente da Internet IPv6 não pode se comunicar diretamente com o endereço IPv6 das VMs.

## <a name="features"></a>Recursos

O suporte nativo a IPv6 para VMs implantadas por meio do Azure Resource Manager fornece:

1. Serviços de IPv6 com balanceamento de carga para clientes IPv6 na Internet
2. Pontos de extremidade IPv4 e IPv6 nativos em VMs ("pilha dupla")
3. Conexões IPv6 nativas iniciadas por entrada e saída
4. Protocolos com suporte, como TCP, UDP e HTTP(S), permitem uma ampla gama de arquiteturas de serviço

## <a name="benefits"></a>Benefícios

Essa funcionalidade permite os seguintes benefícios principais:

* Atender as normas governamentais que exigem que os novos aplicativos sejam acessíveis somente aos clientes IPv6
* Permitir que desenvolvedores móveis e de IOT (Internet das coisas) usem Máquinas Virtuais do Azure com pilha dupla (IPv4 + IPv6) para atender aos mercados móveis & IOT em expansão

## <a name="details-and-limitations"></a>Detalhes e limitações

Detalhes

* O serviço DNS do Azure contém registros de nome AAAA IPv4 e IPv6 e responde com os dois registros para o balanceador de carga. O cliente escolhe com qual endereço (IPv4 ou IPv6) quer se comunicar.
* Quando uma VM inicia uma conexão com um dispositivo conectado com a Internet IPv6 pública, o endereço IPv6 de origem da VM é o endereço de rede convertido (NAT) para o endereço IPv6 público do balanceador de carga.
* As VMs que executam o sistema operacional Linux devem ser configuradas para receber um endereço IP IPv6 por meio de DHCP. Muitas das imagens do Linux na Galeria do Azure já estão configuradas para oferecer suporte a IPv6 sem modificação. Para saber mais, confira [Configuração de DHCPv6 em VMs do Linux](load-balancer-ipv6-for-linux.md)
* Se você optar por usar uma investigação de integridade com o balanceador de carga, crie uma investigação de IPv4 e use-a com pontos de extremidade IPv4 e IPv6. Se o serviço em sua VM falhar, os pontos de extremidade IPv4 e IPv6 são tirados do rodízio.

Limitações

* Você não pode adicionar regras de balanceamento de carga do IPv6 no Portal do Azure. As regras só podem ser criadas por meio de modelo, da CLI e do PowerShell.
* Você não pode atualizar VMs existentes para usar endereços IPv6. Você deve implantar novas VMs.
* Um único endereço IPv6 pode ser atribuído a uma única interface de rede em cada VM.
* Os endereços IPv6 públicos não podem ser atribuídos a uma VM. Eles podem ser atribuídos somente a um balanceador de carga.
* Não é possível configurar a pesquisa reversa de DNS para endereços IPv6 públicos.
* As VMs com os endereços IPv6 não podem ser membros de um Serviço de Nuvem do Azure. Elas podem ser conectadas a uma Rede Virtual do Azure (VNet) e se comunicarem entre si por meio de seus endereços IPv4.
* Endereços IPv6 privados podem ser implantados em VMs individuais em um grupo de recursos, mas não podem ser implantados em um grupo de recursos por meio de Conjuntos de Escala.
* As VMs do Azure não podem se conectar via IPv6 com outras VMs, outros serviços do Azure ou dispositivos locais. Eles só podem se comunicar com o Azure Load Balancer via IPv6. No entanto, elas podem se comunicar com esses outros recursos usando IPv4.
* Há suporte para proteção de NSG (grupo de segurança de rede) para IPv4 em implantações de pilha dupla (IPv4+IPv6). Os NSGs não se aplicam aos pontos de extremidade IPv6.
* O ponto de extremidade IPv6 na VM não é exposto diretamente à internet. Ele fica atrás de um balanceador de carga. Somente as portas especificadas nas regras do balanceador de carga são acessíveis via IPv6.
* **No momento, não há suporte** para mudar o parâmetro IdleTimeout para IPv6. O padrão é de quatro minutos.
* **No momento, não há suporte** para alterar o parâmetro loadDistributionMethod para IPv6.
* **No momento, não há suporte** para IPs IPv6 reservados (em que IPAllocationMethod = estático).
* O NAT64 (conversão de IPv6 para IPv4) não é compatível.

## <a name="next-steps"></a>Próximas etapas

Saiba como implantar um balanceador de carga com IPv6.

* [Disponibilidade do IPv6 por região](https://go.microsoft.com/fwlink/?linkid=828357)
* [Implantar um balanceador de carga com IPv6 usando um modelo](load-balancer-ipv6-internet-template.md)
* [Implantar um balanceador de carga com IPv6 usando o Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Implantar um balanceador de carga com IPv6 usando CLI do Azure](load-balancer-ipv6-internet-cli.md)

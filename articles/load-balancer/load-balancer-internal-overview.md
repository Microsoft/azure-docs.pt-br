---
title: "Visão geral do balanceador de carga interno | Microsoft Docs"
description: "Visão geral do balanceador de carga interno e seus recursos. Como um balanceador de carga funciona no Azure e possíveis cenários para configurar pontos de extremidade internos"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ec07c77119c2da408da21fbdc7877d0b43d16556
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="internal-load-balancer-overview"></a>Visão geral do balanceador de carga interno

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ao contrário do balanceador de carga voltado para a Internet, o ILB (Balanceador de Carga Interno) direciona o tráfego somente para os recursos dentro do serviço de nuvem ou usa VPN para acessar a infraestrutura do Azure. A infraestrutura restringe o acesso aos VIPs (endereços IP virtuais) de balanceamento de carga de um Serviço de nuvem ou uma Rede virtual, de modo que nunca será exposta diretamente a um ponto de extremidade da Internet. Isso permite que aplicativos LOB (linha de negócios) internos sejam executados no Azure e acessados na nuvem ou nos recursos locais.

## <a name="why-you-may-need-an-internal-load-balancer"></a>Por que talvez seja necessário balanceador de carga interno

O ILB (Balanceamento de Carga Interno) do Azure fornece balanceamento de carga entre máquinas virtuais que residem em um serviço de nuvem ou em uma rede virtual com escopo regional. Para obter informações sobre o uso e a configuração de redes virtuais com escopo regional, consulte [Redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) no blog do Azure. Redes virtuais existentes configuradas para um grupo de afinidade não podem usar o ILB.

O ILB habilita os seguintes tipos de balanceamento de carga:

* Dentro de um serviço de nuvem, de máquinas virtuais a um conjunto de máquinas virtuais que residem no mesmo serviço de nuvem (veja a Figura 1).
* Em uma rede virtual, de máquinas virtuais na rede virtual a um conjunto de máquinas virtuais que residem no mesmo serviço de nuvem da rede virtual (veja a Figura 2).
* Para uma rede virtual entre instalações, de computadores locais a um conjunto de máquinas virtuais que residem no mesmo serviço de nuvem da rede virtual (veja a Figura 3).
* Aplicativos para a Internet de diversas camadas, cujas camadas de back-end não são para a Internet, mas exigem balanceamento de carga para tráfego da camada para a Internet.
* Balanceamento de carga para aplicativos LOB (linha de negócios) hospedados no Azure sem exigir hardware ou software do balanceador de carga adicional. Incluindo servidores locais, no conjunto de computadores, cujo tráfego é de balanceamento de carga.

## <a name="internet-facing-multi-tier-applications"></a>Aplicativos para a Internet de diversas camadas

A camada Web tem pontos de extremidade para a Internet para clientes de Internet e faz parte de um conjunto de balanceamento de carga. O balanceador de carga distribui o tráfego de entrada de clientes Web para a porta TCP 443 (HTTPS) até os servidores Web.

Os servidores de banco de dados estão protegidos por um ponto de extremidade ILB que os servidores Web usam para armazenamento. Esse ponto de extremidade de balanceamento de carga do serviço de banco de dados, cujo tráfego é de carga balanceada entre servidores de banco de dados do conjunto ILB.

A imagem a seguir mostra o aplicativo para a Internet de várias camadas no mesmo serviço de nuvem.

![Serviço de nuvem único de balanceamento de carga interno](./media/load-balancer-internal-overview/IC736321.png)

Figura 1 – Aplicativo para a Internet de várias camadas

Outro uso possível para um aplicativo com diversas camadas é quando o ILB é implantado em um serviço de nuvem diferente daquele que consome o serviço para o ILB.

Serviços de nuvem que usam a mesma rede virtual terão acesso ao ponto de extremidade ILB. A imagem mostra que os servidores Web front-end estão em um serviço de nuvem diferente do back-end do banco de dados e estão usando o ponto de extremidade ILB dentro da mesma rede virtual.

![Balanceamento de carga interno entre serviços de nuvem](./media/load-balancer-internal-overview/IC744147.png)

Figura 2 – Servidores front-end em um serviço de nuvem diferente

## <a name="intranet-line-of-business-applications"></a>Aplicativos de linha de negócios intranet

Tráfego de clientes em redes locais tem balanceamento de carga em um conjunto de servidores LOB que usam conexão VPN com a rede do Azure.

O computador cliente terá acesso a um endereço IP do serviço de VPN do Azure que usa a VPN de ponto a site. Permite o uso do aplicativo LOB hospedado atrás do ponto de extremidade ILB.

![Balanceamento de carga interno usando VPN ponto a site](./media/load-balancer-internal-overview/IC744148.png)

Figura 3 – Aplicativos LOB hospedados atrás do ponto de extremidade LB

Outro cenário para LOB é ter uma VPN site a site para a rede virtual na qual o ponto de extremidade ILB foi configurado. Isso permite que o tráfego de rede local seja roteado para o ponto de extremidade ILB.

![Balanceamento de carga interno usando VPN site a site](./media/load-balancer-internal-overview/IC744150.png)

Figura 4 – Tráfego de rede local roteado para o ponto de extremidade ILB

## <a name="limitations"></a>Limitações

As configurações do Balanceador de Carga Interno não dão suporte a SNAT. No contexto deste documento, SNAT refere-se à conversão de endereços de rede de origem simulada de porta.  Isso se aplica a cenários em que a VM em um pool de balanceador de carga precisa alcançar o endereço IP de front-end do respectivo balanceador de carga interno. Não há suporte para cenário no balanceador de carga interno. Falhas de conexão ocorrerão quando a carga do fluxo for balanceada para a VM que originou o fluxo. Você deve usar um balanceador de carga de estilo de proxy para esses cenários.

## <a name="next-steps"></a>Próximas etapas

[Suporte do Azure Resource Manager para o Azure Load Balancer](load-balancer-arm.md)

[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

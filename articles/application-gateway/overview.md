---
title: O que é o Gateway de Aplicativo do Azure
description: Saiba como você pode usar um gateway de aplicativo do Azure para gerenciar o tráfego da Web para seu aplicativo.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 52bc2a20be0d7113583b7f23cc569dbb9503d364
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397239"
---
# <a name="what-is-azure-application-gateway"></a>O que é o Gateway de Aplicativo do Azure?

O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web. Os balanceadores de carga tradicionais operam na camada de transporte (camada OSI 4 – TCP e UDP) e encaminham o tráfego com base no endereço IP de origem e na porta para um endereço IP de destino e uma porta.

O Gateway de Aplicativo pode tomar decisões de roteiros com base em outros atributos de uma solicitação HTTP, por exemplo, o caminho de URI ou os cabeçalhos de host. Por exemplo, você pode encaminhar o tráfego com base na URL de entrada. Portanto, se `/images` estiver na URL de entrada, você poderá encaminhar o tráfego para um conjunto específico de servidores (conhecido como um pool) configurado para as imagens. Se `/video` estiver na URL, esse tráfego será encaminhado para outro pool otimizado para vídeos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Esse tipo de roteamento é conhecido como balanceamento de carga da camada de aplicativo (camada OSI 7). O Gateway de Aplicativo do Azure pode fazer o roteamento baseado em URL e muito mais.

>[!NOTE]
> O Azure fornece um conjunto de soluções de balanceamento de carga totalmente gerenciadas para seus cenários. Se você precisar de alto desempenho, baixa latência e balanceamento de carga de camada 4, confira [O que é Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Se você estiver procurando por balanceamento de carga DNS global, confira [O que é o Gerenciador de Tráfego?](../traffic-manager/traffic-manager-overview.md) Os cenários de ponta a ponta podem se beneficiar da combinação dessas soluções.
>
> Para obter uma comparação das opções de balanceamento de carga do Azure, confira [Visão geral das opções de balanceamento de carga no Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="features"></a>Recursos

Para saber mais sobre os recursos do Gateway de Aplicativo, confira [Recursos do Gateway de Aplicativo do Azure](features.md).

## <a name="pricing-and-sla"></a>Preço e SLA

Para obter informações sobre preços do Gateway de Aplicativo, confira [Preços do Gateway de Aplicativo](https://azure.microsoft.com/pricing/details/application-gateway/).

Para obter informações sobre SLA do Gateway de Aplicativo, confira [SLA do Gateway de Aplicativo](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="whats-new"></a>Novidades

Para conhecer as novidades do Gateway de Aplicativo do Azure, confira [Atualizações do Azure](https://azure.microsoft.com/updates/?category=networking&query=Application%20Gateway).

## <a name="next-steps"></a>Próximas etapas

Dependendo dos requisitos e do ambiente, você pode criar um Gateway de Aplicativo de teste usando o portal do Azure, o Azure PowerShell ou a CLI do Azure.

- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure](quick-create-portal.md)
- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – Azure PowerShell](quick-create-powershell.md)
- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – CLI do Azure](quick-create-cli.md)
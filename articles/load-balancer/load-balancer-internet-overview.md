---
title: "Visão geral do balanceador de carga para a Internet | Microsoft Docs"
description: "Visão geral do balanceador de carga para a Internet e seus recursos. Como um balanceador de carga funciona no Azure usando máquinas virtuais e serviços de nuvem."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 529b37aa-a45c-41d1-8877-fee8cc1fa375
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5b9ffeadf6b1ffc4eaf4f49b85ba752c27da0e46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="internet-facing-load-balancer-overview"></a>Visão geral do balanceador de carga para a Internet

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

O balanceador de carga do Azure mapeia o endereço IP público e o número da porta do tráfego de entrada até o endereço IP privado e o número da porta da máquina virtual e vice-versa no tráfego de resposta da máquina virtual. As regras de balanceamento de carga permitem que você distribua tipos específicos de tráfego entre várias máquinas virtuais ou serviços. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web ou funções web.

Para um serviço de nuvem que contenha instâncias de funções web ou funções de trabalho, você pode definir um ponto de extremidade público no arquivo de definição de serviço (.csdef).

O arquivo *servicedefinition.csdef* contém a configuração do ponto de extremidade e quando existem várias instâncias de função para uma implantação de função de trabalho ou da Web, o balanceador de carga é instalado para ele. A maneira de adicionar instâncias à sua implantação de nuvem está alterando a contagem de instâncias no arquivo de configuração de serviço (.csfg).

## <a name="example-of-an-internet-facing-load-balancer"></a>Exemplo de um Load Balancer voltado para a Internet

A figura a seguir mostra um ponto de extremidade de balanceamento de carga para tráfego na Web compartilhado entre três máquinas virtuais para a porta TCP pública e privada de número 80. Essas três máquinas virtuais estão em um conjunto de balanceamento de carga.

![exemplo de balanceador de carga público](./media/load-balancer-internet-overview/IC727496.png)

Figura 1 – Ponto de extremidade com balanceamento de carga para tráfego da Web

Quando os clientes da Internet enviam solicitações de página da Web para o endereço IP público do serviço de nuvem na porta TCP 80, o Azure Load Balancer distribui as solicitações entre as três máquinas virtuais no conjunto de balanceamento de carga. Para obter mais informações sobre os algoritmos do balanceador de carga, consulte a [load balancer overview page (página de visão geral do balanceador de carga)](load-balancer-overview.md#load-balancer-features).

Por padrão, Azure Load Balancer distribui o tráfego de rede igualmente entre várias instâncias da máquina virtual. Também é possível configurar a afinidade de sessão. Para obter mais informações, consulte [load balancer distribution mode (modo de distribuição do balanceador de carga)](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Balanceadores de carga internos](load-balancer-internal-overview.md) para entender melhor qual balanceador de carga é mais adequado para sua implantação na nuvem.

Também é possível [começar a criar um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento específico de tráfego de rede do balanceador de carga.

Se seu aplicativo precisar manter conexões ativas para servidores por trás de um balanceador de carga, você poderá entender mais sobre as [configurações de tempo limite de TCP ocioso para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Assim, você saberá mais sobre o comportamento da conexão ociosa quando estiver usando um Balanceador de Carga do Azure.

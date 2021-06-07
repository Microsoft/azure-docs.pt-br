---
title: Rede e conectividade da Solução VMware no Azure
description: Descrição da conectividade e da rede da Solução VMware no Azure.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462568"
---
<!-- Used in introduction.md and concepts-networking.md -->

A Solução VMware no Azure oferece um ambiente de nuvem privada acessível de recursos locais e baseados no Azure. Serviços, como o Azure ExpressRoute, as conexões VPN ou a WAN Virtual do Azure, fornecem a conectividade. Esses serviços exigem intervalos de endereços de rede e portas de firewall específicos para habilitar os serviços.

Quando uma nuvem privada é implantada, as redes privadas para gerenciamento, provisionamento e vMotion são criadas. Use essas redes privadas para acessar o vCenter e o NSX-T Manager, bem como o vMotion ou a implantação da máquina virtual.  

O Alcance Global do ExpressRoute é usado para conectar nuvens privadas aos ambientes locais. A conexão requer uma rede virtual com um circuito de ExpressRoute para local na assinatura.

As VMs (máquinas virtuais) na nuvem privada podem ser acessadas pela Internet por meio da funcionalidade de IP público da WAN Virtual do Azure.  Por padrão, o acesso à Internet é desabilitado para novas nuvens privadas. Para saber mais, confira [Como usar a funcionalidade de IP público na Solução VMware no Azure](../public-ip-usage.md).


---
title: Rede e conectividade da Solução VMware no Azure
description: Descrição da conectividade e da rede da Solução VMware no Azure.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92924968"
---
<!-- Used in introduction.md and concepts-networking.md -->

A Solução VMware no Azure oferece um ambiente de nuvem privada acessível de ambientes ou recursos locais e baseados no Azure. Serviços, como o Azure ExpressRoute e as conexões VPN, fornecem a conectividade. Esses serviços exigem intervalos de endereços de rede e portas de firewall específicos para habilitar os serviços.

Quando uma nuvem privada é implantada, as redes privadas para gerenciamento, provisionamento e vMotion são criadas. Use essas redes privadas para acessar o vCenter e o NSX-T Manager, bem como o vMotion ou a implantação da máquina virtual.  O Alcance Global do ExpressRoute é usado para conectar nuvens privadas aos ambientes locais. A conexão requer uma rede virtual com um circuito de ExpressRoute na assinatura.

Recursos, como servidores Web e máquinas virtuais, podem ser acessados pela Internet por meio da funcionalidade de IP público do WAN Virtual do Azure.  Por padrão, o acesso à Internet é desabilitado para novas nuvens privadas. Para saber mais, confira [Como usar a funcionalidade de IP público na Solução VMware no Azure](../public-ip-usage.md).
---
title: Parceiros e locais de WAN virtual do Azure | Microsoft Docs
description: Este artigo contém uma lista de parceiros de WAN virtual do Azure e locais de Hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: cherylmc
ms.custom: references_regions
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 4a02a02eeb40c1a61df88c49bf967b2ca57befc3
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489320"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Parceiros WAN Virtual e localizações de hub virtual

Este artigo fornece informações sobre regiões e parceiros virtuais com suporte da WAN para conectividade com o Hub virtual.

A WAN Virtual do Azure é um serviço de rede que fornece conectividade otimizada e automatizada entre branches por meio do Azure. A WAN Virtual permite que você conecte e configure dispositivos de branch para se comunicar com o Azure. A conexão e a configuração podem ser feitas manualmente ou usando dispositivos de provedor por meio de um parceiro de WAN virtual. O uso de dispositivos de parceiros permite facilitar o uso, simplificar a conectividade e o gerenciamento de configuração.

A conectividade do dispositivo local é estabelecida em uma forma automatizada para o Hub Virtual. Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos de extremidade de serviço para habilitar a conectividade de sua rede local (vpnsite). Você só pode ter um hub por região.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Automação de conectividade IPsec de ramificação de parceiros

Dispositivos que se conectam à WAN Virtual do Azure têm automação interna para conexão. Normalmente, isso é definido na interface do usuário de gerenciamento do dispositivo (ou equivalente), que configura a conectividade e o gerenciamento de configuração entre o dispositivo de branch VPN para um ponto de extremidade de VPN de Hub Virtual do Azure (gateway de VPN).

A automação de alto nível seguinte é configurada no centro de gerenciamento/console do dispositivo:

* Permissões apropriadas para o dispositivo para acessar o grupo de recursos da WAN Virtual do Azure
* Carregamento de dispositivo de branch na WAN Virtual do Azure
* Download automático de informações de conectividade do Azure
* Configuração de dispositivo de branch local 

Alguns parceiros de conectividade podem estender a automação para incluir a criação do Gateway de VPN e VNet do Hub Virtual do Azure. Se você quiser saber mais sobre a automação, consulte [diretrizes de automação para parceiros de WAN virtual](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Parceiros de conectividade IPsec de ramificação

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Os parceiros a seguir são candidatos a nosso roteiro com base em uma folha de termos assinada entre as empresas que indicam o escopo do trabalho para automatizar a conectividade IPsec entre o dispositivo de parceiro e os gateways de VPN de WAN virtual do Azure: 128 Technologies, Arista, F5 Networks, Oracle SD-WAN (Talari) e SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Parceiros com ofertas de Hub virtual integradas

Além de ter uma conectividade IPsec de filial automatizada, alguns parceiros oferecem **NVAs (soluções de virtualização de rede)** que podem ser integradas diretamente no Hub de WAN virtual do Azure.  Isso permite aos clientes a opção de encerrar suas conexões de ramificação em um dispositivo de terceiros compatível no Hub virtual.  

Os parceiros que oferecem NVA no Hub de WAN virtual devem:

* Implementar a automação de conectividade IPsec de seu dispositivo de ramificação e ter integrado sua oferta NVA ao Hub de WAN virtual do Azure.
* Ter uma oferta de dispositivo virtual de rede existente disponível no Azure Marketplace.

Se você for um parceiro e tiver dúvidas sobre o NVA gerenciado na oferta do Hub virtual, entre em contato conosco enviando um email para vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>Parceiros NVA de Hub virtual integrados

Esses parceiros têm ofertas de **aplicativos gerenciados** que estão disponíveis agora para serem implantados no Hub Wan virtual.

|Parceiros|Guia de configuração/instruções/implantação|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Guia de implantação do Barracuda CloudGen WAN](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[Roteador do serviço de nuvem da Cisco (CSR) VWAN](https://aka.ms/ciscoMarketPlaceOffer)| Durante a visualização pública do CSR (serviços de nuvem da Cisco) no Hub VWAN, a Cisco exige que o cliente final se registre como um cliente Cisco TEF (avaliação de campo inicial) enviando um email para vwan_public_preview@external.cisco.com e solicitando o guia de implantação do vManage. 
|[VMware SD – WAN no Hub de WAN virtual](https://sdwan.vmware.com/partners/microsoft) | Durante a visualização pública do VMware SD-WAN no VWAN Hub, o VMware exige que o cliente se registre enviando um email para vhubsupport@vmware.com . [VMware SD – WAN no guia de implantação do Hub WAN virtual](https://kb.vmware.com/s/article/82746)|

Os parceiros a seguir são candidatos a trazer o NVA nas ofertas de Hub virtual em breve: Aviatrix, Citrix e versas redes.

## <a name="locations"></a><a name="locations"></a>Localizações

[!INCLUDE [Virtual WAN regions file](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, consulte as [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md).

* Para obter mais informações sobre como automatizar a conectividade com a WAN virtual do Azure, consulte [diretrizes de automação para parceiros de WAN virtual](virtual-wan-configure-automation-providers.md).

---
title: 'Azure ExpressRoute: pré-requisitos'
description: Esta página fornece uma lista dos requisitos a serem atendidos antes que você pode solicitar um circuito de Azure ExpressRoute. Ela inclui uma lista de verificação.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 1fad1bca18d16ac3b6a654a3c289d0a14e3cd2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204785"
---
# <a name="expressroute-prerequisites--checklist"></a>Pré-requisitos e lista de verificação do ExpressRoute
Para se conectar aos serviços de nuvem da Microsoft usando o ExpressRoute, você precisa verificar se os requisitos listados nas seções a seguir foram atendidos.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Conta do Azure
* Uma conta válida e ativa do Microsoft Azure. Esta conta é necessária para configurar o circuito do ExpressRoute. Os circuitos do ExpressRoute são recursos das assinaturas do Azure. Uma assinatura do Azure é um requisito, mesmo se a conectividade estiver limitada a serviços de nuvem da Microsoft não Azure, como Microsoft 365.
* Uma assinatura do Active Microsoft 365 (se estiver usando serviços Microsoft 365). Para obter mais informações, consulte a seção Microsoft 365 requisitos específicos deste artigo.

## <a name="connectivity-provider"></a>Provedor de conectividade

* Você pode trabalhar com um [parceiro de conectividade de ExpressRoute](expressroute-locations.md#partners) para se conecta à nuvem da Microsoft. Você pode configurar uma conexão entre sua rede local e a Microsoft de [três maneiras](expressroute-introduction.md).
* Se seu provedor não for um parceiro de conectividade do ExpressRoute, você ainda poderá se conectar à nuvem da Microsoft por meio de um [provedor de troca de nuvem](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisitos de rede
* **Redundância em cada local de emparelhamento**: a Microsoft requer que sessões BGP redundantes sejam configuradas entre os roteadores da Microsoft e os roteadores de emparelhamento em cada circuito do ExpressRoute (mesmo quando você tem apenas [uma conexão física com uma troca de nuvem](expressroute-faqs.md#onep2plink)).
* **Redundância para recuperação de desastre**: a Microsoft recomenda enfaticamente que você configure pelo menos dois circuitos de ExpressRoute em diferentes locais de emparelhamento para evitar um único ponto de falha.
* **Roteamento**: dependendo de como você se conecta ao Microsoft Cloud, você ou seu provedor precisarão configurar e gerenciar as sessões BGP para [domínios de roteamento](expressroute-circuit-peerings.md). Alguns provedores de conectividade Ethernet ou os provedores de troca de nuvem podem oferecer gerenciamento BGP como um serviço de valor agregado.
* **NAT**: a Microsoft só aceita endereços IP públicos por meio de emparelhamento da Microsoft. Se você estiver usando endereços IP privados em sua rede local, você ou seu provedor precisará converter os endereços IP privados em endereços IP públicos [usando NAT](expressroute-nat.md).
* **QoS**: o Skype for Business tem vários serviços (por exemplo: voz, vídeo, texto) que exigem tratamento diferenciado de QoS. Você e seu provedor devem seguir os [requisitos de QoS](expressroute-qos.md).
* **Segurança de rede**: considere a [segurança de rede](/azure/cloud-adoption-framework/reference/networking-vdc) ao se conectar ao Microsoft Cloud pelo ExpressRoute.

## <a name="microsoft-365"></a>Microsoft 365
Se você planeja habilitar o Microsoft 365 no ExpressRoute, examine os documentos a seguir para obter mais informações sobre os requisitos de Microsoft 365.

* [Azure ExpressRoute para Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Roteamento com o ExpressRoute para Microsoft 365](/microsoft-365/enterprise/routing-with-expressroute)
* [Alta disponibilidade e failover com o ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Microsoft 365 URLs e intervalos de endereços IP](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Planejamento de rede e ajuste de desempenho para Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)
* [Planejamento de rede e migração para Microsoft 365](/microsoft-365/enterprise/network-and-migration-planning)
* [Integração de Microsoft 365 com ambientes locais](/microsoft-365/enterprise/microsoft-365-integration)
* [Vídeos de treinamento avançados do ExpressRoute no Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).
* Localize um provedor de conectividade do ExpressRoute. Consulte [Parceiros e locais de emparelhamento do ExpressRoute](expressroute-locations.md).
* Consulte os requisitos para [Roteamento](expressroute-routing.md), [NAT](expressroute-nat.md)e [QoS](expressroute-qos.md).
* Configurar sua conexão do ExpressRoute.
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurar o roteamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
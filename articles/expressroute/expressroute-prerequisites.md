---
title: 'Pré-requisitos - ExpressRoute: Azure | Microsoft Docs'
description: Esta página fornece uma lista dos requisitos a serem atendidos antes que você pode solicitar um circuito de Azure ExpressRoute. Ela inclui uma lista de verificação.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f16f797afca6d60029c1f39fea7235ce84e4f954
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442206"
---
# <a name="expressroute-prerequisites--checklist"></a>Pré-requisitos e lista de verificação do ExpressRoute
Para se conectar aos serviços de nuvem da Microsoft usando o ExpressRoute, você precisa verificar se os requisitos listados nas seções a seguir foram atendidos.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Conta do Azure
* Uma conta válida e ativa do Microsoft Azure. Esta conta é necessária para configurar o circuito do ExpressRoute. Os circuitos do ExpressRoute são recursos das assinaturas do Azure. Uma assinatura do Azure será um requisito mesmo se a conectividade estiver limitada a serviços de nuvem da Microsoft que não sejam do Azure, como os serviços do Office 365 e o Dynamics 365.
* Uma assinatura ativa do Office 365 (se estiver usando os serviços do Office 365). Para mais informações, confira a seção Requisitos específicos do Office 365 deste artigo.

## <a name="connectivity-provider"></a>Provedor de conectividade

* Você pode trabalhar com um [parceiro de conectividade de ExpressRoute](expressroute-locations.md#partners) para se conecta à nuvem da Microsoft. Você pode configurar uma conexão entre sua rede local e a Microsoft de [três maneiras](expressroute-introduction.md).
* Se seu provedor não for um parceiro de conectividade do ExpressRoute, você ainda poderá se conectar à nuvem da Microsoft por meio de um [provedor de troca de nuvem](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisitos de rede
* **Redundância em cada local de emparelhamento**: A Microsoft exige que sessões BGP redundantes sejam configuradas entre os roteadores da Microsoft e os roteadores de emparelhamento em cada circuito do ExpressRoute (mesmo quando você tem apenas [uma conexão física com uma troca de nuvem](expressroute-faqs.md#onep2plink)).
* **Redundância para recuperação de desastres**: A Microsoft recomenda enfaticamente que você configure pelo menos dois circuitos de ExpressRoute em diferentes locais de emparelhamento para evitar um ponto único de falha.
* **Roteamento**: dependendo de como você se conecta ao Microsoft Cloud, você ou seu provedor precisarão configurar e gerenciar as sessões BGP para [domínios de roteamento](expressroute-circuit-peerings.md). Alguns provedores de conectividade Ethernet ou os provedores de troca de nuvem podem oferecer gerenciamento BGP como um serviço de valor agregado.
* **NAT**: a Microsoft só aceita endereços IP públicos por meio de emparelhamento da Microsoft. Se você estiver usando endereços IP privados em sua rede local, você ou seu provedor precisará converter os endereços IP privados em endereços IP públicos [usando NAT](expressroute-nat.md).
* **QoS**: o Skype for Business tem vários serviços (por exemplo: voz, vídeo, texto) que exigem tratamento diferenciado de QoS. Você e seu provedor devem seguir os [requisitos de QoS](expressroute-qos.md).
* **Segurança de rede**: considere a [segurança de rede](../best-practices-network-security.md) ao se conectar ao Microsoft Cloud pelo ExpressRoute.

## <a name="office-365"></a>Office 365
Se você planeja habilitar o Office 365 no ExpressRoute, consulte os documentos a seguir para saber mais sobre os requisitos do Office 365.

* [Visão geral do ExpressRoute para o Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Roteamento com o ExpressRoute para o Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Alta disponibilidade e failover com o ExpressRoute](https://aka.ms/erhighavailability)
* [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Planejamento da rede e ajuste de desempenho para Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Ferramentas e calculadoras da largura de banda da rede](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integração do Office 365 com ambientes locais](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Vídeos de treinamento avançados do ExpressRoute no Office 365](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
Se você planeja habilitar o Dynamics 365 no ExpressRoute, examine as [URLs do dynamics 365](https://support.microsoft.com/kb/2655102) e os [intervalos de endereços IP](https://support.microsoft.com/kb/2728473).

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).
* Localize um provedor de conectividade do ExpressRoute. Consulte [Parceiros e locais de emparelhamento do ExpressRoute](expressroute-locations.md).
* Consulte os requisitos de [Roteamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar sua conexão do ExpressRoute.
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurar o roteamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)

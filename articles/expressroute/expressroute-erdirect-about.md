---
title: Sobre o ExpressRoute Direct – Azure | Microsoft Docs
description: Esta página fornece uma visão geral do ExpressRoute direta
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e598cc03a1b7b4999719152540866c7168130e03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807473"
---
# <a name="about-expressroute-direct"></a>Sobre o ExpressRoute Direct

O ExpressRoute Direct fornece a capacidade de conectar-se diretamente à rede global da Microsoft em localizações de emparelhamento estrategicamente distribuídas no mundo todo. Diretos do ExpressRoute fornece Gbps duplos de 100 ou 10 Gbps conectividade, que dá suporte à conectividade de ativo/ativo em escala.

Os principais recursos que o ExpressRoute Direct fornece incluem, mas não estão limitados a:

* Ingestão de dados em massa em serviços como Armazenamento e o Cosmos DB
* Isolamento físico para setores regulamentados e que requerem conectividade dedicada e isolada, como: bancos, governo e varejo
* Controle granular de distribuição de circuito com base em unidade de negócios

## <a name="onboard-to-expressroute-direct"></a>Integrar ao diretos do ExpressRoute

Antes de usar diretos do ExpressRoute, você deve primeiro registrar sua assinatura. Para se inscrever, envie um e-mail para <ExpressRouteDirect@microsoft.com> com seu ID de assinatura, incluindo os seguintes detalhes:

* Cenários que você pretende para realizar com o **ExpressRoute Direct**
* Preferências de localização. Confira [Localizações de emparelhamento e parceiros do ExpressRoute](expressroute-locations-providers.md) para obter uma lista completa de todas as localizações
* Linha do tempo para implementação
* Tem mais perguntas?

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute usando um provedor de serviços e ExpressRoute Direct

| **ExpressRoute usando um provedor de serviços** | **ExpressRoute Direct** | 
| --- | --- |
| Utiliza provedores de serviços para permitir integração e conectividade rápidas na infraestrutura existente | Requer 100 Gbps/10 Gbps infraestrutura e gerenciamento completo de todas as camadas
| Integra-se a centenas de fornecedores, inclusive Ethernet e MPLS | Capacidade direta/dedicada para setores regulamentados e ingestão de dados em massa |
| SKUs de circuitos de 50 Mbps a 10 Gbps | Cliente pode selecionar uma combinação do circuito seguinte SKUs no ExpressRoute de 100 Gbps direta: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Cliente pode selecionar uma combinação do circuito seguinte SKUs de ExpressRoute de 10 Gbps direta:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Otimizado para um único locatário | Otimizado para um único locatário/provedores de serviços de nuvem/várias unidades de negócios

## <a name="expressroute-direct-circuits"></a>Circuitos do ExpressRoute Direct

O Microsoft Azure ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o Dynamic 365.  

Cada localização de emparelhamento tem acesso à rede global da Microsoft e pode acessar qualquer região em uma zona geopolítica por padrão e todas as regiões globais com um circuito Premium.  

A funcionalidade na maioria dos cenários é equivalente a circuitos que utilizam um provedor de serviço do ExpressRoute para a operação. Para dar suporte a ainda mais granularidade e às novas funcionalidades oferecidas usando o ExpressRoute Direct, há determinadas funcionalidades principais que existem nos Circuitos do ExpressRoute Direct.

## <a name="circuit-skus"></a>SKUs de circuito

O ExpressRoute Direct dá suporte a cenários de ingestão de dados em massa no Armazenamento do Azure e em outros serviços de Big Data. Circuitos de ExpressRoute em 100 Gbps ExpressRoute direta também dão suporte a **40 Gbps** e **100 Gbps** SKUs do circuito. Os pares de porta física são **100 ou 10 Gbps** apenas e pode ter vários circuitos virtuais. Tamanhos de circuito:

| **Diretos do ExpressRoute de 100 Gbps** | **Diretos do ExpressRoute de 10 Gbps** | 
| --- | --- |
| **Assinado da largura de banda**: 200 Gbps | **Assinado da largura de banda**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisitos técnicos

* Microsoft Enterprise Edge Router (MSEE) Interfaces:
    * Portas duplas de Gigabit Ethernet de 10 ou 100 somente entre o par de roteador
    * Conectividade de fibra de LR de modo único
    * IPv4 e IPv6
    * Bytes de 1500 de MTU de IP

* Conectividade de 2/camada 3 da camada de comutador/roteador:
    * Deve oferecer suporte a marca de 1 802.1Q (Dot1Q) ou uma marca duas 802.1Q (QinQ) encapsulamento de marca
    * EtherType = 0x8100
    * Deve adicionar a marca VLAN externa (STAG) com base na ID de VLAN especificada pela Microsoft - *aplicável apenas em QinQ*
    * Deve oferecer suporte a várias sessões BGP (VLANs) por porta e o dispositivo
    * Conectividade IPv4 e IPv6. *Para IPv6 sem sub-interface adicional será criado. Endereço IPv6 será adicionado ao existente sub-interface*. 
    * Opcional: [Detecção de encaminhamento bidirecional (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) suporte, que é configurado por padrão em todos os Peerings privados em circuitos de ExpressRoute

## <a name="vlan-tagging"></a>Marcação de VLAN

O ExpressRoute Direct dá suporte à marcação de VLAN QinQ e Dot1Q.

* **Marcação de VLAN QinQ** permite domínios de roteamento isolados com base no circuito do ExpressRoute. O Azure aloca dinamicamente uma marca S na criação do circuito e isso não pode ser alterado. Cada emparelhamento no circuito (privado e Microsoft) usará uma marca C exclusiva como a VLAN. A marca C não precisa ser exclusiva nos circuitos nas portas do ExpressRoute Direct.

* **Marcação de VLAN Dot1Q** permite a marcação de uma única VLAN com base no par de portas do ExpressRoute Direct. Uma marca C usada em um emparelhamento precisa ser exclusiva em todos os circuitos e emparelhamentos do par de portas do ExpressRoute Direct.

## <a name="workflow"></a>Fluxo de trabalho

[![Fluxo de trabalho](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Contrato de Nível de Serviço

O ExpressRoute Direct fornece o mesmo SLA de nível empresarial com conexões redundantes ativas/ativas à rede global da Microsoft. A infraestrutura do ExpressRoute é redundante e a conectividade com a rede global da Microsoft é redundante, diversificada e pode ser dimensionada de acordo com os requisitos do cliente. 

## <a name="next-steps"></a>Próximas etapas

[Configurar o ExpressRoute Direct](expressroute-howto-erdirect.md)

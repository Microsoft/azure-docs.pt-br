---
title: Sobre o Azure ExpressRoute Direct
description: Saiba mais sobre os principais recursos do Azure ExpressRoute Direct e as informações necessárias para carregar o ExpressRoute Direct, como SKUs disponíveis e requisitos técnicos.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: duau
ms.openlocfilehash: 0365fd8d0d04b7d144ca6826d3d5eff976558eb3
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202132"
---
# <a name="about-expressroute-direct"></a>Sobre o ExpressRoute Direct

O ExpressRoute Direct fornece a capacidade de conectar-se diretamente à rede global da Microsoft em localizações de emparelhamento estrategicamente distribuídas no mundo todo. O ExpressRoute Direct fornece conectividade dual de 100 Gbps ou 10 Gbps, que dá suporte à conectividade ativa/ativa em escala.

Os principais recursos que o ExpressRoute Direct fornece incluem, mas não estão limitados a:

* Ingestão de dados em massa em serviços como Armazenamento e o Cosmos DB
* Isolamento físico para indústrias que são regulamentadas e exigem conectividade dedicada e isolada, como: bancos, governo e varejo
* Controle granular de distribuição de circuito com base em unidade de negócios

## <a name="onboard-to-expressroute-direct"></a>Integração ao ExpressRoute Direct

Antes de usar o ExpressRoute Direct, você deve primeiro registrar sua assinatura. Para se inscrever, envie um e-mail para <ExpressRouteDirect@microsoft.com> com seu ID de assinatura, incluindo os seguintes detalhes:

* Cenários que você pretende para realizar com o **ExpressRoute Direct**
* Preferências de localização. Confira [Localizações de emparelhamento e parceiros do ExpressRoute](expressroute-locations-providers.md) para obter uma lista completa de todas as localizações
* Linha do tempo para implementação
* Tem mais perguntas?

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute usando um provedor de serviços e ExpressRoute Direct

| **ExpressRoute usando um provedor de serviços** | **ExpressRoute Direct** | 
| --- | --- |
| Utiliza provedores de serviços para permitir integração e conectividade rápidas na infraestrutura existente | Requer infraestrutura de 100 Gbps/10 Gbps e gerenciamento completo de todas as camadas
| Integra-se a centenas de fornecedores, inclusive Ethernet e MPLS | Capacidade direta/dedicada para setores regulamentados e ingestão de dados em massa |
| SKUs de circuitos de 50 Mbps a 10 Gbps | O cliente pode selecionar uma combinação das seguintes SKUs de circuito no ExpressRoute direto de 100 Gbps: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> O cliente pode selecionar uma combinação dos seguintes SKUs de circuito no ExpressRoute direto de 10 Gbps:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Otimizado para um único locatário | Otimizado para locatário único com várias unidades de negócios e vários ambientes de trabalho

## <a name="expressroute-direct-circuits"></a>Circuitos do ExpressRoute Direct

O Microsoft Azure ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como Microsoft Azure e Microsoft 365.

Cada localização de emparelhamento tem acesso à rede global da Microsoft e pode acessar qualquer região em uma zona geopolítica por padrão e todas as regiões globais com um circuito Premium.  

A funcionalidade na maioria dos cenários é equivalente a circuitos que utilizam um provedor de serviço do ExpressRoute para a operação. Para dar suporte a ainda mais granularidade e às novas funcionalidades oferecidas usando o ExpressRoute Direct, há determinadas funcionalidades principais que existem nos Circuitos do ExpressRoute Direct.

## <a name="circuit-skus"></a>SKUs de circuito

O ExpressRoute Direct dá suporte a cenários de ingestão de dados em massa no Armazenamento do Azure e em outros serviços de Big Data. Os circuitos do ExpressRoute em 100 Gbps ExpressRoute Direct agora também dão suporte a SKUs de circuito de **40 Gbps** e **100 Gbps** . Os pares de porta física são de **100 ou 10 Gbps** apenas e podem ter vários circuitos virtuais. Tamanhos de circuito:

| **100 Gbps ExpressRoute direto** | **ExpressRoute direto de 10 Gbps** | 
| --- | --- |
| **Largura de banda assinada**: 200 Gbps | **Largura de banda assinada**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisitos técnicos

* Interfaces de roteador do Microsoft Enterprise Edge (MSEE):
    * Duas portas Ethernet de 10 ou 100 Gigabit somente entre o par de roteador
    * Conectividade de fibra de modo único LR
    * IPv4 e IPv6
    * IP MTU 1500 bytes

* Conectividade de camada de comutador/roteador 2/camada 3:
    * Deve oferecer suporte à marca 1 802.1 Q (Dot1Q) ou a um encapsulamento de marca 802.1 Q (QinQ) de duas marcas
    * EtherType = 0x8100
    * É necessário adicionar a tag VLAN externa (STAG) com base na ID de VLAN especificada pela Microsoft- *aplicável somente em QinQ*
    * Deve oferecer suporte a várias sessões BGP (VLANs) por porta e dispositivo
    * Conectividade IPv4 e IPv6. *Para IPv6, nenhuma subinterface adicional será criada. O endereço IPv6 será adicionado à subinterface existente*. 
    * Opcional: suporte a [BFD (detecção de encaminhamento bidirecional)](./expressroute-bfd.md) , que é configurado por padrão em todos os emparelhamentos privados em circuitos do ExpressRoute

## <a name="vlan-tagging"></a>Marcação de VLAN

O ExpressRoute Direct dá suporte à marcação de VLAN QinQ e Dot1Q.

* **Marcação de VLAN QinQ** permite domínios de roteamento isolados com base no circuito do ExpressRoute. O Azure aloca dinamicamente uma marca S na criação do circuito e isso não pode ser alterado. Cada emparelhamento no circuito (privado e Microsoft) usará uma marca C exclusiva como a VLAN. A marca C não precisa ser exclusiva nos circuitos nas portas do ExpressRoute Direct.

* **Marcação de VLAN Dot1Q** permite a marcação de uma única VLAN com base no par de portas do ExpressRoute Direct. Uma marca C usada em um emparelhamento precisa ser exclusiva em todos os circuitos e emparelhamentos do par de portas do ExpressRoute Direct.

## <a name="workflow"></a>Fluxo de trabalho

[![modelo](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

O ExpressRoute Direct fornece o mesmo SLA de nível empresarial com conexões redundantes ativas/ativas à rede global da Microsoft. A infraestrutura do ExpressRoute é redundante e a conectividade com a rede global da Microsoft é redundante, diversificada e pode ser dimensionada de acordo com os requisitos do cliente. 

## <a name="next-steps"></a>Próximas etapas

[Configurar o ExpressRoute Direct](expressroute-howto-erdirect.md)
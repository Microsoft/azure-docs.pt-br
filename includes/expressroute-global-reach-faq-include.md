---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171658"
---
### <a name="what-is-expressroute-global-reach"></a>O que é o Alcance Global do ExpressRoute?

O Alcance Global do ExpressRoute é um serviço do Azure que conecta as redes locais por meio do serviço ExpressRoute por meio da rede global da Microsoft. Por exemplo, se você tiver um data center particular na Califórnia conectado ao ExpressRoute no Vale do Silício e outro data center particular no Texas conectado ao ExpressRoute em Dallas, com o Alcance Global do ExpressRoute, você poderá conectar seus data centers particulares juntos por meio das duas conexões do ExpressRoute e o tráfego dos data centers percorrerá o backbone da rede da Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Como fazer para habilitar ou desabilitar o Alcance Global do ExpressRoute?

Habilite o Alcance Global do ExpressRoute conectando os circuitos do ExpressRoute. Desabilite o recurso desconectando os circuitos. Consulte a [configuração](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>O ExpressRoute Premium é necessário para o Alcance Global do ExpressRoute?

Se os circuitos do ExpressRoute estiverem na mesma região geopolítica, você não precisará do ExpressRoute Premium para conectá-los. Se dois circuitos do ExpressRoute estiverem em regiões geopolíticas diferentes, você precisará do ExpressRoute Premium para ambos os circuitos, a fim de permitir a conectividade entre eles. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Como serei cobrado pelo Alcance Global do ExpressRoute?

O ExpressRoute permite a conectividade da rede local com os serviços em nuvem da Microsoft. O Alcance Global do ExpressRoute permite a conectividade entre suas próprias redes locais por meio de circuitos do ExpressRoute existentes, aproveitando a rede global da Microsoft. O Alcance Global do ExpressRoute é cobrado separadamente do serviço ExpressRoute existente. Há uma tarifa de Complemento para habilitar esse recurso em cada circuito do ExpressRoute. O tráfego entre as redes locais habilitadas pelo Alcance Global do ExpressRoute será cobrado por uma taxa de saída na origem e por uma taxa de entrada no destino. As taxas são baseadas na zona em que os circuitos estão localizados.

### <a name="where-is-expressroute-global-reach-supported"></a>Quais países dão suporte ao Alcance Global do ExpressRoute?

O alcance Global do ExpressRoute tem suporte no [selecionar países/regiões ou locais](../articles/expressroute/expressroute-global-reach.md). Os circuitos de ExpressRoute devem ser criados nos locais nesses países/regiões ou locais de emparelhamento.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Tenho mais de duas redes locais, cada uma conectada a um circuito do ExpressRoute. Posso habilitar o Alcance Global do ExpressRoute para conectar todas as redes locais?

Sim, você pode, desde que os circuitos estão em países/regiões com suporte. Você precisa conectar dois circuitos do ExpressRoute por vez. Para criar uma rede de malha completa, você precisa enumerar todos os pares de circuitos e repetir a configuração. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Posso habilitar o Alcance Global do ExpressRoute entre dois circuitos do ExpressRoute no mesmo local de emparelhamento?

Não. Os dois circuitos precisam vir de locais de emparelhamento diferentes. Se um metro em um país/região com suporte tem mais de um local de emparelhamento do ExpressRoute, você pode conectar juntos os circuitos do ExpressRoute criados em diferentes locais de emparelhamento que metro. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Se o Alcance Global do ExpressRoute estiver habilitado entre o circuito X e o circuito Y, e entre o circuito Y e o circuito Z, minhas redes locais conectadas ao circuito X e ao circuito Z se comunicarão por meio da rede da Microsoft?

Não. Para habilitar a conectividade entre duas redes locais, é necessário conectar os circuitos do ExpressRoute correspondentes explicitamente. No exemplo acima, é necessário conectar o circuito X e o circuito Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Qual é a taxa de transferência de rede que posso esperar entre minhas redes locais depois de habilitar o Alcance Global do ExpressRoute?

A taxa de transferência de rede entre as redes locais, habilitadas pelo Alcance Global do ExpressRoute, é limitada pelo menor dos dois circuitos do ExpressRoute. Tráfego local para o Azure e-local para compartilham o mesmo circuito e estão sujeitos ao limite de largura de banda da mesma. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Com o alcance Global do ExpressRoute, quais são os limites no número de rotas que posso anunciar e o número de rotas que vou receber?

O número de rotas que você pode anunciar à Microsoft sobre o emparelhamento privado do Azure permanece em 4000 em um circuito padrão ou 10000 em um circuito Premium. O número de rotas que você receberá da Microsoft no emparelhamento privado do Azure será a soma das rotas de suas redes virtuais do Azure e as rotas de suas outras redes locais conectadas por meio do ExpressRoute o alcance Global. Verifique se que você definir um limite de prefixo máximo apropriado em seu roteador local. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Qual é o SLA para o alcance global do ExpressRoute?

Alcance Global de ExpressRoute fornece os mesmos [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) como o serviço de ExpressRoute regular.

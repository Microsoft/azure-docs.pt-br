---
title: incluir arquivo
description: arquivo de inclusão
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/29/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1f6c65d6d8da275d6c52ac2802092dbb5e4e507d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "73182165"
---
### <a name="what-is-expressroute-direct"></a>O que é o ExpressRoute Direct?

O ExpressRoute Direct fornece aos clientes a capacidade de conectar-se diretamente à rede global da Microsoft em locais de emparelhamento distribuídos estrategicamente no mundo todo. O ExpressRoute Direct oferece conectividade dupla de 100 ou 10 Gbps, compatível com conectividade ativa/ativa em escala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Como os clientes se conectam ao ExpressRoute Direct? 

Os clientes precisarão trabalhar com suas operadoras locais e fornecedores de colocalização para obter conectividade com os roteadores do ExpressRoute para usar o ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Quais locais atualmente são compatíveis com o ExpressRoute Direct? 

Verifique a disponibilidade na [página de localização](../articles/expressroute/expressroute-locations-providers.md). 

### <a name="what-is-the-sla-for-expressroute-direct"></a>O que é o SLA para ExpressRoute Direct?

O ExpressRoute Direct utilizará o mesmo [nível empresarial do ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quais cenários os clientes devem considerar com o ExpressRoute Direct?  

O ExpressRoute Direct oferece aos clientes pares de portas diretas de 100 ou 10 Gbps no backbone global da Microsoft. Os cenários que oferecerão os melhores benefícios aos clientes incluem: ingestão de dados em massa, isolamento físico para mercados regulados e capacidade dedicada para cenários intermitentes, como renderização. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual é o modelo de cobrança para ExpressRoute Direct? 

O ExpressRoute Direct será cobrado um valor fixo pelo par de portas. Circuitos padrão serão incluídos sem horas adicionais e o premium terá um pequeno encargo por complemento. A saída será cobrada por circuito na zona do local de emparelhamento.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Quando começa a cobrança pelos pares de portas do ExpressRoute Direct?

Os pares de portas do ExpressRoute Direct são cobrados 45 dias a partir da criação do recurso ou quando um ou os dois links são habilitados, o que vier primeiro. O período de cortesia de 45 dias é concedido para permitir aos clientes concluir o processo de conexão cruzada com o provedor de colocação.

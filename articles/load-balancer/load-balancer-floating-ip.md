---
title: Azure Load Balancer configuração de IP flutuante
description: Visão geral de Azure Load Balancer IP flutuante
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94699399"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer configuração de IP flutuante

O balanceador de carga fornece várias funcionalidades para os aplicativos UDP e TCP.

## <a name="floating-ip"></a>IP flutuante

Alguns cenários de aplicativos preferem ou exigem a utilização da mesma porta por várias instâncias do aplicativo em uma VM no pool de back-end. Exemplos comuns de reutilização de porta incluem clusters de alta disponibilidade, dispositivos virtuais de rede e exposição de vários pontos de extremidade TLS sem nova criptografia. Se você quiser reutilizar a porta de back-end em várias regras, habilite o IP Flutuante na definição da regra.

**IP flutuante** é a terminologia do Azure para uma parte do que é conhecido como DSR (Retorno de Servidor Direto). O DSR consiste em duas partes:

- Topologia de fluxo
- Um esquema de mapeamento de endereço IP

Em um nível de plataforma, o Azure Load Balancer sempre opera em uma topologia de fluxo de DSR, independentemente de o IP Flutuante estar habilitado ou não. Isso significa que a parte de saída de um fluxo é sempre reescrita corretamente para fluir diretamente de volta para a origem.
Com o IP Flutuante, o Azure expõe um esquema de mapeamento de endereço IP de balanceamento de carga tradicional para facilitar o uso (o IP de instâncias de VM). A habilitação do IP Flutuante altera o mapeamento de endereço IP para o IP de front-end do balanceador de carga para possibilitar flexibilidade adicional. Saiba mais [aqui](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Limitações

- O IP flutuante não tem suporte atualmente em configurações de IP secundários para cenários de balanceamento de carga

## <a name="next-steps"></a>Próximas etapas

- Confira [Criar um Standard Load Balancer público](quickstart-load-balancer-standard-public-portal.md) para começar a usar um Load Balancer.
- Aprenda sobre [Conexões de saída do Azure Load Balancer](load-balancer-outbound-connections.md).
- Saiba mais sobre o [Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [Investigações de Integridade](load-balancer-custom-probe-overview.md).
- Saiba mais sobre o [Diagnóstico do Load Balancer Standard](load-balancer-standard-diagnostics.md).
- Saiba mais sobre [Grupos de Segurança de Rede](../virtual-network/network-security-groups-overview.md).
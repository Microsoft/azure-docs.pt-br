---
title: Porta frontal do Azure-proteção contra DDoS
description: Esta página fornece informações sobre como a porta frontal do Azure ajuda a proteger contra ataques de DDoS
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350776"
---
# <a name="ddos-protection-on-front-door"></a>Proteção contra DDoS na porta frontal

A porta frontal do Azure tem vários recursos e características que podem ajudar a impedir ataques de DDoS (negação de serviço distribuído). Esses recursos podem impedir que os invasores atinjam seu aplicativo e afetem a disponibilidade e o desempenho do seu aplicativo.

## <a name="integration-with-azure-ddos-protection-basic"></a>Integração com a proteção contra DDoS do Azure básica

A porta frontal é protegida pela proteção contra DDoS do Azure básica. Ele é integrado à plataforma de porta de front-end por padrão e sem custo adicional. A escala completa e a capacidade da rede implantada globalmente da porta frontal fornecem defesa contra ataques comuns de camada de rede por meio de monitoramento de tráfego AlwaysOn e mitigação em tempo real. A proteção contra DDoS básica também defende contra as inundações de consulta de DNS de camada 7 mais comuns, com frequência ocorrendo e os ataques de camada 3 e 4 volumétricos que visam pontos de extremidade públicos. Esse serviço também tem um registro comprovado na proteção dos serviços corporativos e de consumidores da Microsoft contra ataques em larga escala. Para obter mais informações, consulte [proteção contra DDoS do Azure](../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Bloqueio de protocolo

A porta frontal só aceita tráfego nos protocolos HTTP e HTTPS e só processará solicitações válidas com um `Host` cabeçalho conhecido. Esse comportamento ajuda a mitigar alguns tipos comuns de ataque de DDoS, incluindo ataques volumétricos que se espalham por uma variedade de protocolos e portas, ataques de amplificação de DNS e ataques de envenenamento de TCP.

## <a name="capacity-absorption"></a>Absorção de capacidade

A porta frontal é um serviço amplamente dimensionado e distribuído globalmente. Temos muitos clientes, incluindo produtos de nuvem de grande escala da Microsoft, que recebem centenas de milhares de solicitações por segundo. A porta frontal está localizada na borda da rede do Azure, absorvendo e isolando geograficamente ataques de volume grande. Isso pode impedir que o tráfego mal-intencionado vá mais do que a borda da rede do Azure.

## <a name="caching"></a>Cache

Os [recursos de cache da porta frontal](./front-door-caching.md) podem ser usados para proteger back-ends de grandes volumes de tráfego gerados por um ataque. Os recursos armazenados em cache serão retornados dos nós de borda da porta frontal para que não sejam encaminhados ao seu back-end. Até mesmo tempos de expiração de cache curtos (segundos ou minutos) em respostas dinâmicas podem reduzir significativamente a carga em serviços de back-end. Para obter mais informações sobre conceitos e padrões de cache, consulte [considerações de cache](/azure/architecture/best-practices/caching) e [padrão de reserva de cache](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Firewall do aplicativo Web (WAF)

O [WAF (firewall do aplicativo Web) da porta frontal](../web-application-firewall/afds/afds-overview.md) pode ser usado para atenuar vários tipos diferentes de ataques:

* O uso do conjunto de regras gerenciadas fornece proteção contra vários ataques comuns.
* O tráfego de fora de uma região geográfica definida ou dentro de uma região definida pode ser bloqueado ou Redirecionado para uma página da Web estática. Para obter mais informações, consulte [filtragem geográfica](../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* Os endereços IP e os intervalos identificados como mal-intencionados podem ser bloqueados.
* A limitação de taxa pode ser aplicada para impedir que endereços IP chamem seu serviço com muita frequência.
* Você pode criar [regras de WAF personalizadas](../web-application-firewall/afds/waf-front-door-custom-rules.md) para bloquear e classificar automaticamente os ataques http ou HTTPS que têm assinaturas conhecidas.

## <a name="for-further-protection"></a>Para maior proteção

Se precisar de mais proteção, você poderá habilitar a [proteção contra DDoS do Azure Standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) na VNet em que os back-ends são implantados. Os clientes padrão de proteção contra DDoS recebem benefícios adicionais, incluindo proteção de custos, garantia de SLA e acesso a especialistas da equipe de resposta rápida de DDoS para obter ajuda imediata durante um ataque.

## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar um [perfil do WAF na porta frontal](front-door-waf.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
---
title: Solucionando problemas de tempos limite de resposta do cliente e erros com o gerenciamento de API
description: Solucionar problemas de erros de conexão intermitente e problemas de latência relacionados no gerenciamento de API
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576524"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Solucionando problemas de tempos limite de resposta do cliente e erros com o gerenciamento de API

Este artigo ajuda você a solucionar problemas de erros de conexão intermitente e problemas de latência relacionados no [Gerenciamento de API do Azure](./api-management-key-concepts.md). Especificamente, este artigo fornecerá informações e solução de problemas para o esgotamento de portas SNAT (conversão de rede de endereço de origem). Se precisar de mais ajuda, entre em contato com os especialistas do Azure no [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/) ou entre em arquivo uma solicitação de suporte com o [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Sintomas

Aplicativos cliente que chamam APIs por meio do serviço de gerenciamento de API (APIM) podem apresentar um ou mais dos seguintes sintomas:

* Erros intermitentes de HTTP 500
* Mensagens de erro de tempo limite

Esses sintomas são manifestados como instâncias do `BackendConnectionFailure` no [Azure monitor logs de recursos](../azure-monitor/essentials/resource-logs.md).

## <a name="cause"></a>Causa

Esse padrão de sintomas geralmente ocorre devido a limites de porta de conversão de endereços de rede (SNAT) com seu serviço APIM.

Sempre que um cliente chama uma de suas APIs do APIM, o serviço de gerenciamento de API do Azure abre uma porta SNAT para acessar sua API de back-end. Conforme discutido em [conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md), o Azure usa o SNAT (conversão de endereço de rede de origem) e um Load Balancer (não exposto aos clientes) para se comunicar com pontos de extremidade fora do Azure no espaço de endereço IP público, bem como pontos de extremidade internos ao Azure que não estão usando [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md). Essa situação só é aplicável a APIs de back-end expostas em IPs públicos.

Inicialmente, cada instância do serviço de gerenciamento de API recebe um número pré-alocado de portas SNAT. Esse limite afeta a abertura de conexões com a mesma combinação de host e porta. As portas SNAT são usadas quando você tem chamadas repetidas para a mesma combinação de endereço e porta. Depois que uma porta SNAT for liberada, a porta estará disponível para reutilização conforme necessário. O balanceador de carga de rede do Azure recupera portas SNAT de conexões fechadas somente após esperar quatro minutos.

Uma rápida sucessão de solicitações de cliente para suas APIs poderá esgotar a cota pré-configurada de portas SNAT se essas portas não forem fechadas e recicladas rápido o suficiente, impedindo que o serviço APIM processe solicitações de cliente em tempo hábil.

## <a name="mitigations-and-solutions"></a>Mitigações e soluções

Resolver o problema do esgotamento da porta SNAT primeiro requer o diagnóstico e a otimização do desempenho de seus serviços de back-end.

As estratégias gerais para mitigar o esgotamento de porta SNAT são discutidas em [solução de problemas de falhas de conexões de saída](../load-balancer/troubleshoot-outbound-connection.md) da documentação do *Azure Load Balancer* . Dessas estratégias, as seguintes são aplicáveis ao gerenciamento de API.

### <a name="scale-your-apim-instance"></a>Dimensionar sua instância do APIM

Cada instância de gerenciamento de API é alocada a um número de portas SNAT, com base em unidades APIMs. Você pode alocar portas SNAT adicionais dimensionando sua instância de gerenciamento de API com unidades adicionais. Para obter mais informações, consulte [dimensionar seu serviço de gerenciamento de API](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> O uso da porta SNAT não está disponível atualmente como uma métrica para unidades de gerenciamento de API de dimensionamento automático.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Usar vários IPs para suas URLs de back-end

Cada conexão de sua instância do APIM com o mesmo IP de destino e porta de destino do seu serviço de back-end usará uma porta SNAT para manter um fluxo de tráfego distinto. Sem diferentes portas SNAT para o tráfego de retorno do serviço em segundo plano, o APIM não teria como separar uma resposta de outra.

Como as portas SNAT podem ser reutilizadas se o IP de destino ou porta de destino forem diferentes, outra maneira de evitar o esgotamento de porta SNAT é usar vários IPs para suas URLs de serviço de back-end.

Para obter mais informações, consulte [proxy de saída Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Coloque seu serviço de back-end e APIM na mesma VNet

Se sua API de back-end estiver hospedada em um serviço do Azure que dá suporte a *pontos de extremidade de serviço* como o serviço de aplicativo, você poderá evitar problemas de esgotamento de porta SNAT colocando sua instância APIM e o serviço de back-end na mesma rede virtual e expondo-as por meio de pontos de extremidade de [serviço](../virtual-network/virtual-network-service-endpoints-overview.md) ou [pontos de extremidade privados](../private-link/private-endpoint-overview.md). Quando você usa uma VNet comum e coloca pontos de extremidade de serviço na sub-rede de integração, o tráfego de saída da instância do APIM para esses serviços ignora a Internet, evitando assim restrições de porta SNAT. Da mesma forma, se você usar uma VNet e pontos de extremidade privados, não terá nenhum problema de porta SNAT de saída para esse destino.

Para obter detalhes, consulte [como usar o gerenciamento de API do Azure com redes virtuais](api-management-using-with-vnet.md) e [integrar o serviço de aplicativo com uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md).

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Coloque seu APIM em uma rede virtual e roteie chamadas de saída para o Firewall do Azure

Assim como colocar seus serviços de APIM e back-end em uma rede virtual, você pode empregar o Firewall do Azure em uma VNet com seu serviço APIM e, em seguida, rotear chamadas APIM de saída para o Firewall do Azure. Entre o APIM e o Firewall do Azure (estando na mesma VNet), nenhuma porta SNAT é necessária. Para conexões SNAT com seus serviços de back-end, o Firewall do Azure tem 64.000 portas disponíveis, uma quantidade muito maior do que é alocada para instâncias APIM.

Consulte a documentação do [Firewall do Azure](../firewall/overview.md) para obter mais informações.

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Considere o cache de resposta e outro ajuste de desempenho de back-end

Outra mitigação potencial a ser considerada é melhorar os tempos de processamento para suas APIs de back-end. Uma maneira de fazer isso é Configurando determinadas APIs com cache de resposta para reduzir a latência entre aplicativos cliente chamando sua API e sua carga de back-end APIM.

Para obter mais informações, consulte [Adicionar Caching para melhorar o desempenho no gerenciamento de API do Azure](api-management-howto-cache.md).

### <a name="consider-implementing-access-restriction-policies"></a>Considere implementar políticas de restrição de acesso

Se fizer sentido para seu cenário de negócios, você poderá implementar políticas de restrição de acesso para seu produto de gerenciamento de API. Por exemplo, a `rate-limit-by-key` política pode ser usada para evitar picos de uso da API por chave, limitando a taxa de chamada por um período de tempo especificado.

Consulte [políticas de restrição de acesso de gerenciamento de API](api-management-access-restriction-policies.md) para obter mais informações.

## <a name="see-also"></a>Veja também

* [Azure Load Balancer: Solucionando problemas de falhas de conexões de saída](../load-balancer/troubleshoot-outbound-connection.md)
* [Serviço de Azure App: Solucionando erros de conexão de saída intermitente](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)

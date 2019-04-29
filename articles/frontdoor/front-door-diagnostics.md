---
title: Monitoramento de métricas e logs no serviço de porta da frente do Azure | Microsoft Docs
description: Este artigo descreve os diferentes métricas e logs de acesso que dá suporte ao serviço de porta da frente do Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 16770ea0a320b3d9f081cc21a102ab050a6467f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736763"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Monitoramento de métricas e logs no serviço de porta da frente do Azure

Usando o serviço de porta da frente do Azure, você pode monitorar os recursos das seguintes maneiras:

- **Métricas**. O Gateway de Aplicativo atualmente tem sete métricas para exibir os contadores de desempenho.
- **Logs**. Atividade e logs de diagnóstico permitem que o desempenho, acesso e outros dados sejam salvos ou consumidos de um recurso para fins de monitoramento.

### <a name="metrics"></a>Métricas

As métricas são um recurso para certos recursos do Azure que permitem que você exibir os contadores de desempenho no portal. Estas são as métricas de porta da frente disponíveis:

| Métrica | Nome de exibição da métrica | Unidade | Dimensões | DESCRIÇÃO |
| --- | --- | --- | --- | --- |
| RequestCount | Contagem de solicitações | Contagem | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de solicitações de cliente atendidas pelo Front Door.  |
| RequestSize | Tamanho da solicitação | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como solicitações de clientes para o Front Door. |
| ResponseSize | Tamanho da resposta | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O número de bytes enviados como respostas do Front Door para clientes. |
| TotalLatency | Latência total | Milissegundos | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | O tempo calculado da solicitação do cliente recebida pela porta da frente até que o cliente confirmadas o último byte de resposta da porta da frente. |
| BackendRequestCount | Contagem de solicitações de back-end | Contagem | HttpStatus</br>HttpStatusGroup</br>Back-end | O número de solicitações enviadas do Front Door aos back-ends. |
| BackendRequestLatency | Latência de solicitação de back-end | Milissegundos | Back-end | O tempo calculado de quando a solicitação foi enviada pelo Front Door ao back-end até o Front Door receber o último byte de resposta do back-end. |
| BackendHealthPercentage | Percentual de integridade do back-end | Porcentagem | Back-end</br>BackendPool | O percentual de investigações de integridade bem-sucedidas do Front Door aos back-ends. |
| WebApplicationFirewallRequestCount | Contagem de solicitações do Firewall de Aplicativo Web | Contagem | PolicyName</br>RuleName</br>Ação | O número de solicitações de cliente processadas pela segurança da camada de aplicativo do Front Door. |

## <a name="activity-log"></a>Logs de atividade

Logs de atividades fornecem informações sobre as operações realizadas no serviço de porta da frente. Eles também determinam o que, quem e quando operações de gravação (put, post ou delete) realizadas no serviço de porta da frente para qualquer.

>[!NOTE]
>Logs de atividades não incluem operações de leitura (get). Eles também não incluem operações executadas por meio do portal do Azure ou a API de gerenciamento original.

Logs de atividades de acesso no seu serviço de porta da frente ou todos os logs dos recursos do Azure no Azure Monitor. Para exibir logs de atividade:

1. Selecione sua instância de porta da frente.
2. Selecione **log de atividades**.

    ![Log de atividades](./media/front-door-diagnostics/activity-log.png)

3. Escolha um escopo de filtragem e, em seguida, selecione **aplicar**.

## <a name="diagnostic-logging"></a>Logs de diagnóstico
Logs de diagnóstico fornecem informações detalhadas sobre as operações e erros que são importantes para auditoria e solução de problemas. Os logs de diagnóstico diferem dos logs de atividade.

Logs de atividades fornecem informações sobre as operações realizadas em recursos do Azure. Os logs de diagnóstico fornecem informações sobre operações que o recurso realizou. Para obter mais informações, consulte [logs de diagnóstico do Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md).

![Logs de diagnóstico](./media/front-door-diagnostics/diagnostic-log.png)

Para configurar logs de diagnóstico para seu serviço de porta da frente:

1. Selecione o serviço do Azure da frente.

2. Escolher **configurações de diagnóstico**.

3. Selecione **Ativar diagnóstico**. Arquivar logs de diagnóstico, juntamente com métricas para uma conta de armazenamento, transmiti-los para um hub de eventos ou enviá-los para os logs do Azure Monitor.

Atualmente, o serviço de porta da frente fornece logs de diagnóstico (agrupados por hora). Os logs de diagnóstico fornecem solicitações individuais de API com cada entrada tem o esquema a seguir:

| Propriedade  | DESCRIÇÃO |
| ------------- | ------------- |
| ClientIp | Endereço IP do cliente que fez a solicitação. |
| ClientPort | A porta do IP do cliente que fez a solicitação. |
| HttpMethod | Método HTTP usado pela solicitação. |
| HttpStatusCode | O código de status HTTP retornado do proxy. |
| HttpStatusDetails | Status resultante na solicitação. Significado deste valor de cadeia de caracteres pode ser encontrado em uma tabela de referência de Status. |
| HttpVersion | Tipo da solicitação ou da conexão. |
| RequestBytes | O tamanho da mensagem de solicitação HTTP em bytes, incluindo os cabeçalhos de solicitação e o corpo da solicitação. |
| RequestUri | URI da solicitação recebida. |
| ResponseBytes | Bytes enviados pelo servidor de back-end, como a resposta.  |
| RoutingRuleName | O nome da regra de roteamento de solicitação correspondente. |
| SecurityProtocol | A versão do protocolo TLS/SSL não usado pela solicitação ou nulo se nenhuma criptografia. |
| TimeTaken | O período de tempo que a ação tomou em milissegundos. |
| UserAgent | O tipo de navegador que o cliente usou |
| TrackingReference | A cadeia de caracteres de referência exclusivo que identifica uma solicitação no servidas por porta da frente, também é enviado como o cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |

## <a name="next-steps"></a>Próximas etapas

- [Criar um perfil de porta da frente](quickstart-create-front-door.md)
- [Como funciona a porta da frente](front-door-routing-architecture.md)

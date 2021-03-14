---
title: 'Métricas no Azure Monitor: Hubs de Eventos do Azure | Microsoft Docs'
description: Este artigo fornece informações sobre como usar o Monitoramento do Azure com Hubs de Eventos do Azure
ms.topic: article
ms.date: 02/25/2021
ms.openlocfilehash: 52ab66fe264b85be117eb8e2e21cb89f38d0fcae
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715574"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Métricas de Hubs de Eventos do Azure no Azure Monitor

As métricas dos hubs de eventos fornecem o estado dos recursos dos hubs de eventos em sua assinatura do Azure. Com um amplo conjunto de dados de métricas, você pode avaliar a integridade geral dos seus hubs de eventos não apenas no nível de namespace, mas também no nível de entidade. Essas estatísticas podem ser importantes, pois elas ajudam você a monitorar o estado de seus hubs de eventos. As métricas também podem ajudar a solucionar problemas de causa raiz sem a necessidade de entrar em contato com o suporte do Azure.

O Azure Monitor fornece interfaces de usuário unificadas para monitoramento entre os diferentes serviços do Azure. Para obter mais informações, consulte [Monitoramento no Microsoft Azure](../azure-monitor/overview.md) e o exemplo [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias maneiras de acessar as métricas. Você pode acessar as métricas por meio do [Portal do Azure](https://portal.azure.com) ou usar as APIs do Azure Monitor (REST e .NET) e as soluções de análise como o Log Analytics e os Hubs de Eventos. Para mais informações, consulte [Monitoramento de dados coletados por Azure Monitor](../azure-monitor/data-platform.md).

As métricas estão habilitadas por padrão e você pode acessar os dados dos últimos 30 dias. Se você precisar manter os dados por um período de tempo maior, poderá arquivar dados de métricas em uma conta de armazenamento do Azure. Essa configuração pode ser definida nas [configurações de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md) no Azure monitor.


## <a name="access-metrics-in-the-portal"></a>Acessar as métricas no portal

É possível monitorar as métricas ao longo do tempo no [Portal do Azure](https://portal.azure.com). O exemplo a seguir mostra como exibir solicitações bem-sucedidas e solicitações de entrada no nível da conta:

![Exibir métricas bem-sucedidas][1]

Você também pode acessar as métricas diretamente por meio do namespace. Para fazer isso, selecione o namespace e, em seguida, selecione **métricas**. Para exibir as métricas filtradas para o escopo do hub de eventos, selecione o Hub de eventos e, em seguida, selecione **métricas**.

Para métricas com suporte a dimensões, será necessário filtrar o valor da dimensão desejado, como mostrado no exemplo a seguir:

![Filtrar com valor de dimensão][2]

## <a name="billing"></a>Cobrança

O uso de métricas no Azure Monitor atualmente é gratuito. No entanto, se você usar outras soluções que ingerirem dados de métricas, você poderá ser cobrado por essas soluções. Por exemplo, você será cobrado pelo Armazenamento do Azure se você arquivar dados de métrica para uma conta de Armazenamento do Azure. Você também será cobrado pelo Azure se você transmitir dados de métricas para Azure Monitor logs para análise avançada.

As métricas a seguir oferecem uma visão geral da integridade do seu serviço. 

> [!NOTE]
> Diversas métricas estão sendo preteridas à medida que são transferidas para um nome diferente. Isso pode exigir que você atualize suas referências. Métricas marcadas com a palavra-chave "preterida" não terão suporte no futuro.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo com suporte para todas as métricas do Hubs de Eventos é 1 minuto.

## <a name="azure-event-hubs-metrics"></a>Métricas dos hubs de eventos do Azure
Para obter uma lista de métricas com suporte do serviço, consulte [hubs de eventos do Azure](../azure-monitor/essentials/metrics-supported.md#microsofteventhubnamespaces)

> [!NOTE]
> Quando ocorre um erro de usuário, os hubs de eventos do Azure atualizam a métrica de **erros do usuário** , mas não registra nenhuma outra informação de diagnóstico. Portanto, você precisa capturar detalhes sobre erros de usuário em seus aplicativos. Ou, você também pode converter a telemetria gerada quando as mensagens são enviadas ou recebidas no Application insights. Para obter um exemplo, consulte [acompanhamento com Application insights](../service-bus-messaging/service-bus-end-to-end-tracing.md#tracking-with-azure-application-insights).

## <a name="azure-monitor-integration-with-siem-tools"></a>Integração do Azure Monitor com as ferramentas SIEM
Rotear seus dados de monitoramento (logs de atividades, logs de diagnóstico e assim por diante) para um hub de eventos com o Azure Monitor permite que você integre facilmente com as ferramentas SIEM (gerenciamento de eventos e informações de segurança). Para obter mais informações, consulte os seguintes artigos/Postagens de blog:

- [Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
- [Introdução à integração de log do Azure](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Usar o Monitor do Azure para integrar às ferramentas de SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

No cenário em que uma ferramenta SIEM consome dados de log de um hub de eventos, se você não vir mensagens de entrada ou se vir mensagens de entrada, mas nenhuma mensagem de saída no grafo de métricas, siga estas etapas:

- Se não houver **nenhuma mensagem de entrada**, significa que o serviço de Azure monitor não está movendo os logs de auditoria/diagnóstico para o Hub de eventos. Abra um tíquete de suporte com a equipe de Azure Monitor neste cenário. 
- Se houver mensagens de entrada, mas **nenhuma mensagem de saída**, significa que o aplicativo Siem não está lendo as mensagens. Entre em contato com o provedor SIEM para determinar se a configuração do hub de eventos desses aplicativos está correta.


## <a name="next-steps"></a>Próximas etapas

* Consulte [Visão geral do Monitoramento do Azure](../azure-monitor/overview.md).
* Exemplo de como [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub. 

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

- Introdução com um Tutorial de Hubs de Eventos
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png

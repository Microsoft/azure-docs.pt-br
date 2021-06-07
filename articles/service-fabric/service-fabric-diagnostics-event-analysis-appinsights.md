---
title: Análise de eventos do Service Fabric do Azure com o Application Insights
description: Saiba mais sobre visualização e análise de eventos utilizando o Application Insights para o monitoramento e diagnóstico de clusters do Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 0e84c3c1ab542dc4541ebdcfbc49f45e02458d9c
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626207"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Visualização e análise de eventos com o Application Insights

Parte do Monitor do Azure, o Application Insights é uma plataforma extensível para monitoramento e diagnóstico de aplicativos. Inclui uma poderosa ferramenta de análise e consulta, visualizações e dashboard personalizável e outras opções, incluindo alertas automatizados. A integração do Application Insights com o Service Fabric inclui experiências de ferramentas para o Visual Studio e o portal do Azure, bem como métricas específicas do Service Fabric, fornecendo uma experiência de registro abrangente e pronta para uso. Embora muitos logs sejam automaticamente criados e coletados para você com o Application Insights, recomendamos que você adicione mais log personalizado aos seus aplicativos para criar uma experiência de diagnóstico mais completa.

Este artigo ajuda a resolver as seguintes questões comuns:

* Como eu sei o que está acontecendo dentro do meu aplicativo e serviços e reúno telemetria?
* Como faço para solucionar meu aplicativo, especialmente os serviços que se comunicam uns com os outros?
* Como faço para obter métricas sobre o desempenho dos meus serviços, por exemplo, o tempo de carregamento da página, as solicitações HTTP?

O objetivo deste artigo é mostrar como obter insights e solucionar problemas no Application Insights. Se você quiser aprender a configurar o Application Insights com o Service Fabric, confira este [tutorial](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitoramento no Application Insights

O Application Insights tem uma experiência avançada ao usar o Service Fabric. Na página de visão geral, o Application Insights fornece informações importantes sobre seu serviço, como o tempo de resposta e o número de solicitações processadas. Ao clicar no botão "Pesquisar" na parte superior, é possível ver uma lista de solicitações recentes no aplicativo. Além disso, você poderá ver solicitações com falha e diagnosticar quais erros podem ter ocorrido.

![Visão geral do Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

No painel direito da imagem anterior, há dois tipos principais de entradas na lista: solicitações e eventos. Solicitações são chamadas feitas à API do aplicativo por meio de solicitações HTTP nesse caso e os eventos são eventos personalizados, que funcionam como telemetria que você pode adicionar em qualquer parte do código. É possível explorar ainda mais a instrumentação dos aplicativos em [API do Application Insights para métricas e eventos personalizados](../azure-monitor/app/api-custom-events-metrics.md). Clicar em uma solicitação exibirá mais detalhes, conforme mostrado na imagem a seguir, incluindo dados específicos para Service Fabric, que são coletados no pacote do Application Insights Service Fabric NuGet. Essas informações são úteis para solucionar problemas e saber qual é o estado do aplicativo e todas essas informações podem ser pesquisadas no Application Insights

![Captura de tela que mostra mais detalhes, incluindo dados específicos para Service Fabric, que são coletados no pacote NuGet do Application Insights Service Fabric.](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

O Application Insights tem uma exibição designada para consultar todos os dados que chegam. Selecione "Metrics Explorer" na parte superior da página de visão geral para navegar até o portal de Application Insights. Aqui, é possível executar consultas em eventos personalizados mencionados anteriormente, solicitações, exceções, contadores de desempenho e outras métricas usando a linguagem de consulta do Kusto. O exemplo a seguir mostra todas as solicitações na última 1 hora.

![Detalhes do pedido de insights do aplicativo](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Para explorar ainda mais os recursos do portal do Application Insights, acesse a documentação do portal [Application Insights](../azure-monitor/app/overview-dashboard.md).

### <a name="configuring-application-insights-with-eventflow"></a>Configurar o Application Insights com EventFlow

Se você estiver utilizando EventFlow para agregar eventos, certifique-se de importar o pacote `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`NuGet. O código a seguir é necessário na seção *saídas* do *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Certifique-se de fazer as alterações necessárias nos filtros, assim como incluir quaisquer outras entradas (em conjunto com os respectivos pacotes NuGet).

## <a name="application-insights-sdk"></a>SDK do Application Insights

É recomendável usar EventFlow e WAD como soluções de agregação, pois elas permitem uma abordagem mais modular para diagnóstico e monitoramento, ou seja, se você quiser alterar suas saídas do EventFlow, não será necessário alterar a sua instrumentação real, apenas uma modificação simples no arquivo de configuração. Se você decidir investir no uso de Application Insights e não tiver probabilidade de mudar para uma plataforma diferente, deverá examinar o uso do Application Insights ' novo SDK para agregar eventos e enviá-los para Application Insights. Isso significa que você não precisará mais configurar o EventFlow para enviar seus dados para o Application Insights, mas, em vez disso, instalará o pacote Service Fabric NuGet do ApplicationInsight. Detalhes sobre o pacote podem ser encontrados [aqui](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[O suporte do Application Insights para micros serviços e contêineres](https://azure.microsoft.com/blog/app-insights-microservices/) mostra alguns dos novos recursos que estão sendo trabalhados (atualmente ainda em beta), que permitem que você tenha opções de monitoramento mais avançadas com o Application Insights. Isso inclui o rastreamento de dependência (usado na criação de um AppMap de todos os seus serviços e aplicativos em um cluster e a comunicação entre eles) e melhor correlação de rastreamentos provenientes de seus serviços (ajuda a identificar melhor um problema no fluxo de trabalho de um aplicativo ou serviço).

Se você estiver desenvolvendo no .NET e provavelmente usará alguns dos modelos de programação do Service Fabric e estiver disposto a usar o Application Insights como sua plataforma para visualizar e analisar dados de eventos e logs, recomendamos que você acesse a rota do SDK do Application Insights como seu fluxo de trabalho de monitoramento e diagnóstico. Leia a documentação [Application insights](../azure-monitor/azure-monitor-app-hub.yml) e [os logs de rastreamento](../azure-monitor/app/asp-net-trace-logs.md) para começar a usar o Application insights para coletar e exibir seus logs.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navegando no recurso Application Insights no portal do Azure

Depois de configurar o Application Insights como uma saída para seus eventos e logs, as informações devem começar a aparecer em seu recurso do Application Insights em alguns minutos. Navegue até o recurso Application Insights, que o levará ao painel de recursos do Application Insights. Selecione **Pesquisar** na barra de tarefas Application insights para ver os rastreamentos mais recentes que ele recebeu e para poder filtrá-los.

*Metrics Explorer* é uma ferramenta útil para criar painéis personalizados com base em métricas que seus aplicativos, serviços e clusters podem estar reportando. Consulte [Exploring Metrics no Application Insights](../azure-monitor/essentials/metrics-charts.md) para configurar alguns gráficos com base nos dados que você está coletando.

Ao clicar em **Análise** você será direcionado para o portal de Análise do Application Insights, onde poderá consultar eventos e rastreamentos com maior escopo e opcionalidade. Leia mais sobre isso em [Análise no Application Insights](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Configurar alertas no AI](../azure-monitor/alerts/alerts-log.md) para ser notificado sobre mudanças no desempenho ou uso
* [Detecção inteligente no Application Insights](../azure-monitor/app/proactive-diagnostics.md) realiza uma análise proativa da telemetria enviada ao Application Insights para avisá-lo de possíveis problemas de desempenho

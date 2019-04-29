---
title: Fazer consulta entre recursos com o Azure Monitor | Microsoft Docs
description: Este artigo descreve como você pode consultar nos recursos de vários workspaces e no aplicativo App Insights em sua assinatura.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: b0d12021be5a5dca348ea3ffa3f0b853725812da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589296"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Executar consultas entre logs de recursos no Azure Monitor  

Anteriormente, com o Azure Monitor, você só podia analisar dados no workspace atual, e isso limitava sua capacidade de consultar vários workspaces definidos em sua assinatura.  Além disso, você pode pesquisar somente itens de telemetria coletados do seu aplicativo baseado na web com o Application Insights diretamente no Application Insights ou do Visual Studio.  Isso também tornou um desafio para analisar nativamente dados de aplicativo e operacionais juntos.   

Agora você pode consultar não apenas em vários workspaces de Application Insights, mas também os dados de um aplicativo Application Insights específico no mesmo grupo de recursos, outro grupo de recursos ou outra assinatura. Isso fornece uma exibição de seus dados de todo o sistema.  Esses tipos de consultas só podem ser realizados no [Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Limites de recursos cruzados consulta 

* O número de recursos do Application Insights que você pode incluir em uma única consulta é limitado a 100.
* Não há suporte para a consulta de recursos cruzados no Designer de exibição. Você pode criar uma consulta no Log Analytics e fixá-lo ao painel do Azure e [visualizar uma pesquisa de log](../../azure-monitor/learn/tutorial-logs-dashboards.md#visualize-a-log-search). 
* Consulta de recursos cruzados em alertas do log tem suporte no novo [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Por padrão, o Azure Monitor usa a [API herdada de alertas do Log Analytics](../platform/api-alerts.md) para a criação de novas regras de alertas de log do portal do Azure, mas você pode mudar para a [API herdada de alertas de log](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Após a mudança, a nova API torna-se o padrão para novas regras de alerta no portal do Azure e permite criar regras de alertas de log de consulta de recursos cruzados. Você pode criar regras de alerta de log de consulta de recursos cruzados sem fazer a mudança por meio de [modelo ARM para scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) – mas essa regra de alerta é gerenciável no entanto [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e não do portal do Azure.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consultar em workspaces do Log Analytics e do Application Insights
Para fazer referência a outro workspace em sua consulta, use o identificador [*workspace*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) e, para um aplicativo do Application Insights, use o identificador [*aplicativo*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression).  

### <a name="identifying-workspace-resources"></a>Identificar recursos do workspace
Os exemplos a seguir demonstram consultas em workspaces do Log Analytics para retornar contagens resumidas de logs da tabela de Atualização em um workspace chamado *contosoretail-it*. 

A identificação de um workspace pode ser realizada de várias maneiras:

* Nome de recurso – é um nome legível do workspace, também conhecido como *nome do componente*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >A identificação de um workspace por nome presume exclusividade em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.

* Nome qualificado – é o "nome completo" do workspace, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID do workspace – uma ID do workspace é o identificador exclusivo e imutável atribuído a cada workspace representado como um identificador global exclusivo (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID de recurso do Azure – a identidade exclusiva definida pelo Azure do workspace. Use a ID do Recurso quando o nome do recurso for ambíguo.  Para workspaces, o formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspacess/componentName*.  

    Por exemplo: 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identificar um aplicativo
Os exemplos a seguir retornam uma contagem resumida de solicitações feitas em relação a um aplicativo chamado *fabrikamapp* no Application Insights. 

A identificação de um aplicativo no Application Insights pode ser realizada com a expressão *app(Identifier)*.  O argumento *Identificador* especifica o aplicativo usando um dos seguintes:

* Nome de recurso – é um nome legível do aplicativo, também conhecido como o *nome do componente*.  

    `app("fabrikamapp")`

* Nome qualificado – é o "nome completo" do aplicativo, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID – o GUID de aplicativo do aplicativo.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* ID de recurso do Azure – a identidade exclusiva definida pelo Azure do aplicativo. Use a ID do Recurso quando o nome do recurso for ambíguo. O formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName*.  

    Por exemplo: 
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Executar uma consulta em vários recursos
Você pode consultar vários recursos de qualquer uma das suas instâncias de recursos, que podem ser espaços de trabalho e aplicativos combinados.
    
Exemplo de consulta em dois workspaces:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Usando consulta entre recursos para vários recursos
Ao usar consultas entre recursos para correlacionar dados de vários workspaces do Log Analytics e recursos do Application Insights, a consulta pode se tornar complexa e difícil de manter. Você deve aproveitar as [funções nas consultas de log do Azure Monitor](functions.md) para separar a lógica de consulta do escopo dos recursos da consulta, o que simplifica a estrutura da consulta. O exemplo a seguir demonstra como você pode monitorar vários recursos do Application Insights e visualizar a contagem de solicitações com falha pelo nome do aplicativo. 

Crie uma consulta como a seguinte que referencia o escopo dos recursos do Application Insights. O comando `withsource= SourceApp` adiciona uma coluna que designa o nome do aplicativo que enviou o log. [Salve a consulta como função](functions.md#create-a-function) com o alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Agora você pode [usar essa função](../../azure-monitor/log-query/functions.md#use-a-function) em uma consulta entre recursos, como a seguir. O alias de função _applicationsScoping_ retorna a união da tabela de solicitações de todos os aplicativos definidos. A consulta então filtra solicitações com falha e visualiza as tendências por aplicativo. O operador _parse_ é opcional neste exemplo. Ele extrai o nome do aplicativo do _SourceApp_ propriedade.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![Gráfico de tempo](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral das consultas de log e de como os dados de log do Azure Monitor estão estruturados, examine [Analisar dados de log no Azure Monitor](log-query-overview.md).
- Para exibir todos os recursos das consultas de log do Azure Monitor, examine [Consultas de log do Azure Monitor](query-language.md).

---
title: Fazer consulta entre recursos com o Azure Monitor | Microsoft Docs
description: Este artigo descreve como você pode consultar nos recursos de vários workspaces e no aplicativo App Insights em sua assinatura.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 1a6b328bc076ffa619921a8698549e95e6f15c60
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400753"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Executar consulta de log em Azure Monitor que abrangem espaços de trabalho e aplicativos

Os logs de Azure Monitor dão suporte à consulta em vários espaços de trabalho Log Analytics e Application Insights aplicativo no mesmo grupo de recursos, outro grupo de recursos ou outra assinatura. Isso fornece uma exibição de seus dados de todo o sistema.

Há dois métodos para consultar dados que são armazenados em vários espaços de trabalho e aplicativos:
1. Explicitamente, especificando os detalhes do espaço de trabalho e do aplicativo. Essa técnica é detalhada neste artigo.
2. Usando implicitamente [consultas de contexto de recurso](../platform/design-logs-deployment.md#access-mode). Quando você consulta no contexto de um recurso específico, um grupo de recursos ou uma assinatura, os dados relevantes serão buscados de todos os espaços de trabalho que contêm dados para esses recursos. Application Insights dados armazenados em aplicativos, não serão buscados.

> [!IMPORTANT]
> Se você estiver usando um [recurso do Application Insights baseado em workspace](../app/create-workspace-resource.md), a telemetria é armazenada no workspace do Log Analytics com todos os outros dados do log. Use a expressão Workspace () para gravar uma consulta que inclui o aplicativo em vários espaços de trabalho. Para vários aplicativos no mesmo espaço de trabalho, você não precisa de uma consulta entre espaços de trabalho.


## <a name="cross-resource-query-limits"></a>Limites de consulta entre recursos 

* O número de recursos de Application Insights e espaços de trabalho de Log Analytics que você pode incluir em uma única consulta é limitado a 100.
* Não há suporte para a consulta entre recursos no Designer de Exibição. Você pode criar uma consulta em Log Analytics e fixá-la no painel do Azure para [Visualizar uma consulta de log](../learn/tutorial-logs-dashboards.md). 
* As consultas entre recursos em alertas de log só têm suporte na [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)atual. Se você estiver usando a API de alertas de Log Analytics herdados, será necessário [alternar para a API atual](../platform/alerts-log-api-switch.md).


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consultar em workspaces do Log Analytics e do Application Insights
Para fazer referência a outro workspace em sua consulta, use o identificador [*workspace*](./workspace-expression.md) e, para um aplicativo do Application Insights, use o identificador [*aplicativo*](./app-expression.md).  

### <a name="identifying-workspace-resources"></a>Identificar recursos do workspace
Os exemplos a seguir demonstram consultas em workspaces do Log Analytics para retornar contagens resumidas de logs da tabela de Atualização em um workspace chamado *contosoretail-it*. 

A identificação de um workspace pode ser realizada de várias maneiras:

* Nome de recurso – é um nome legível do workspace, também conhecido como *nome do componente*. 

    `workspace("contosoretail-it").Update | count`

* Nome qualificado – é o "nome completo" do espaço de trabalho, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionname/resourcegroup/ComponentName*. 

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

    >[!NOTE]
    >A identificação de um aplicativo por nome pressupõe exclusividade em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.

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
Você pode consultar vários recursos de qualquer uma das suas instâncias de recursos, que podem ser workspaces e aplicativos combinados.
    
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



Agora você pode [usar essa função](./functions.md#use-a-function) em uma consulta entre recursos, como a seguir. O alias de função _applicationsScoping_ retorna a união da tabela de solicitações de todos os aplicativos definidos. A consulta então filtra solicitações com falha e visualiza as tendências por aplicativo. O operador _parse_ é opcional neste exemplo. Ele extrai o nome do aplicativo do _SourceApp_ propriedade.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Esse método não pode ser usado com alertas de log porque a validação de acesso dos recursos de regra de alerta, incluindo espaços de trabalho e aplicativos, é executada no momento da criação do alerta. Não há suporte para a adição de novos recursos à função após a criação do alerta. Se preferir usar a função para o escopo de recursos em alertas de log, você precisará editar a regra de alerta no portal ou com um modelo do Resource Manager para atualizar os recursos com escopo. Como alternativa, você pode incluir a lista de recursos na consulta de alerta de log.


![Gráfico de tempo](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral das consultas de log e de como os dados de log do Azure Monitor estão estruturados, examine [Analisar dados de log no Azure Monitor](log-query-overview.md).


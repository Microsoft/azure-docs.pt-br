---
title: Consultar dados em Azure Monitor usando o Data Explorer do Azure (versão prévia)
description: Use o Azure Data Explorer para executar consultas entre produtos entre o Azure Data Explorer, espaços de trabalho Log Analytics e aplicativos Application Insights clássicos no Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: a31ef69d84f64e4bcaa46adac26a29d2cc367351
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731693"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Consultar dados no Azure Monitor usando o Azure Data Explorer (Versão Prévia)

O Data Explorer do Azure dá suporte a consultas entre serviços entre o Azure Data Explorer, [Application insights (ia)](../app/app-insights-overview.md)e [log Analytics (la)](./data-platform-logs.md). Em seguida, você pode consultar seu espaço de trabalho Log Analytics/Application Insights usando as ferramentas de Data Explorer do Azure e consultá-lo em uma consulta entre serviços. O artigo mostra como fazer uma consulta entre serviços e como adicionar o espaço de trabalho Log Analytics/Application Insights à interface do usuário da Web do Azure Data Explorer.

O fluxo de consultas entre serviços do Azure Data Explorer: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="fluxo de proxy do Azure data Explorer.":::

> [!NOTE]
> * A capacidade de consultar Azure Monitor dados do Data Explorer do Azure, diretamente das ferramentas de cliente do Azure Data Explorer, ou indiretamente, executando uma consulta em um cluster de Data Explorer do Azure, está no modo de visualização.
>* Entre em contato com a equipe de [consulta entre serviços](mailto:adxproxy@microsoft.com) com perguntas.

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Adicionar um workspace do Log Analytics/Application Insights às ferramentas de cliente do Azure Data Explorer

1. Verifique se o seu cluster nativo do Azure Data Explorer (como o cluster *help*) aparece no menu à esquerda antes de se conectar ao cluster do Log Analytics ou do Application Insights.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Cluster nativo do Azure Data Explorer.":::

 Na interface do usuário do Azure Data Explorer (https://dataexplorer.azure.com/clusters) ), selecione **Adicionar Cluster**.

2. Na janela **Adicionar Cluster**, adicione a URL do cluster do LA ou do AI.

    * Para LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Para AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selecione **Adicionar**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Adicionar cluster.":::
 
>[!NOTE]
>Se você adicionar uma conexão a mais de um espaço de trabalho do Log Analytics/Application insights, dê a cada um um nome diferente. Caso contrário, eles terão o mesmo nome no painel esquerdo.

 Depois que a conexão for estabelecida, o seu workspace do Log Analytics ou do Application Insights aparecerá no painel esquerdo com o seu cluster do Azure Data Explorer nativo.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics e clusters de Data Explorer do Azure.":::
 
> [!NOTE]
> O número de workspaces do Azure Monitor que podem ser mapeados é limitado a 100.

## <a name="create-queries-using-azure-monitor-data"></a>Criar consultas usando dados de Azure Monitor

Você pode executar as consultas usando as ferramentas de cliente que dão suporte a consultas do Azure Data Explorer, como: Kusto Explorer, Interface do Usuário da Web do Azure Data Explorer, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens, API REST.

> [!NOTE]
> A capacidade de consulta entre serviços é usada apenas para recuperação de dados. Para obter mais informações, confira [Capacidade de suporte da função](#function-supportability).

> [!TIP]
> * O nome do banco de dados deve ter o mesmo nome que o recurso especificado na consulta entre serviços. Os nomes diferenciam minúsculas e maiúsculas.
> * Em consultas entre clusters, verifique se a nomenclatura de aplicativos do Application Insights e dos workspaces do Log Analytics está correta.
> * Se os nomes contiverem caracteres especiais, eles serão substituídos pela codificação de URL na consulta de serviço cruzado.
> * Se os nomes incluírem caracteres que não atendem às [regras de nome do identificador KQL](/azure/data-explorer/kusto/query/schema-entities/entity-names), eles serão substituídos pelo caractere de traço **-** .

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Consulta direta nos seus workspaces do Log Analytics ou do Application Insights com as ferramentas de cliente do Azure Data Explorer

Execute consultas em seus espaços de trabalho Log Analytics ou Application Insights. Verifique se o workspace está selecionado no painel esquerdo.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Consultar Log Analytics espaço de trabalho.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Consulta cruzada de seu Log Analytics ou Application Insights e o cluster nativo do Azure Data Explorer

Ao executar consultas de serviço entre clusters, verifique se o cluster nativo do Azure Data Explorer está selecionado no painel esquerdo. Os exemplos a seguir demonstram a combinação de tabelas de cluster Data Explorer do Azure [usando Union](/azure/data-explorer/kusto/query/unionoperator) com o espaço de trabalho log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Consulta entre serviços do Data Explorer do Azure.":::

Usar o [operador `join`](/azure/data-explorer/kusto/query/joinoperator), em vez do union, pode exigir que um [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) execute-o em um cluster nativo do Azure Data Explorer.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unir dados de um cluster do Azure Data Explorer em um locatário com um recurso do Azure Monitor em outro locatário

Não há suporte para consultas entre locatários entre os serviços. Você está conectado a um locatário para executar a consulta que abrange ambos os recursos.

Se o recurso do Azure Data Explorer estiver no Locatário 'A' e o workspace do Log Analytics estiver no Locatário 'B', use um dos dois seguintes métodos:

1. O Azure Data Explorer permite que você adicione funções para entidades de segurança em locatários diferentes. Adicione a sua ID de usuário no Locatário 'B' como um usuário autorizado no cluster do Azure Data Explorer. Valide a propriedade *['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster)* no cluster do Azure Data Explorer que contém o Locatário 'B'. Execute a consulta cruzada totalmente no Locatário 'B'.

2. Use o [Lighthouse](../../lighthouse/index.yml) para projetar o recurso do Azure Monitor no Locatário 'A'.
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Conectar-se aos clusters do Azure Data Explorer de diferentes locatários

O Azure Data Explorer o conecta automaticamente no locatário no qual a conta de usuário pertence originalmente. Para acessar os recursos em outros locatários com a mesma conta de usuário, o `tenantId` precisa ser especificado explicitamente na cadeia de conexão: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Capacidade de suporte da função

As consultas de serviço cruzado Data Explorer do Azure dão suporte a funções para Application Insights e Log Analytics.
Essa funcionalidade permite que as consultas entre clusters façam referência a uma função tabular do Azure Monitor diretamente.
Os comandos a seguir têm suporte com a consulta de serviço cruzado:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

A imagem a seguir ilustra um exemplo de consulta de uma função tabular da IU da Web do Azure Data Explorer.
Para usar a função, execute o nome na janela Consulta.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Consultar uma função de tabela do Azure Data Explorer interface do usuário da Web.":::

## <a name="additional-syntax-examples"></a>Exemplos de sintaxe adicionais

As seguintes opções de sintaxe estão disponíveis ao chamar os clusters Log Analytics ou Application Insights:

|Descrição da sintaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| O banco de dados em um cluster que contém apenas o recurso definido nesta assinatura (**recomendado para consultas entre clusters**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| O cluster que contém todos os aplicativos/workspaces nesta assinatura    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|O cluster que contém todos os aplicativos/workspaces na assinatura e que são membros deste grupo de recursos    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|O cluster que contém apenas o recurso definido nesta assinatura      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre a [estrutura de dados dos espaços de trabalho log Analytics e Application insights](data-platform-logs.md).
- Aprenda a [escrever consultas no Azure data Explorer](/azure/data-explorer/write-queries).
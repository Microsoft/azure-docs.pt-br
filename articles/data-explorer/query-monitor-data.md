---
title: Consultar dados no Azure Monitor usando o Gerenciador de dados do Azure (visualização)
description: Neste tópico, consultar dados no Azure Monitor, criando um proxy de Gerenciador de dados do Azure para consultas de produto cruzado com o Application Insights e Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811187"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Consultar dados no Azure Monitor usando o Gerenciador de dados do Azure (visualização)

O cluster de proxy de Gerenciador de dados do Azure (Proxy ADX) é uma entidade que permite que você execute consultas de produto cruzado entre o Data Explorer do Azure [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview), e [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) no [Do azure Monitor](/azure/azure-monitor/) service. Você pode mapear espaços de trabalho do Log Analytics do Azure Monitor ou aplicativos do Application Insights como um cluster de proxy. Em seguida, você pode consultar o cluster de proxy usando as ferramentas do Data Explorer do Azure e consultá-lo em uma consulta de cluster entre. O artigo mostra como conectar a um cluster de proxy, adicione um cluster de proxy à interface do usuário da Web do Azure do Gerenciador de dados e executar consultas em seus aplicativos de AI ou a LA espaços de trabalho do Data Explorer do Azure.

O fluxo de proxy de Gerenciador de dados do Azure: 

![Fluxo de proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> O Proxy de ADX está no modo de visualização. Para habilitar esse recurso, entre em contato com o [ADXProxy](mailto:adxproxy@microsoft.com) equipe.

## <a name="connect-to-the-proxy"></a>Conectar-se ao proxy

1. Verifique se o seu cluster nativo do Gerenciador de dados do Azure (como *ajudar* cluster) é exibido no menu à esquerda antes de se conectar ao seu cluster do Log Analytics ou Application Insights.

    ![Cluster ADX nativo](media/adx-proxy/web-ui-help-cluster.png)

1. Na IU do Gerenciador de dados do Azure (https://dataexplorer.azure.com/clusters), selecione **Adicionar Cluster**.

1. No **Adicionar Cluster** janela:

    * Adicione a URL para o cluster LA ou AI. Por exemplo: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Selecione **Adicionar**.

    ![Adicionar cluster](media/adx-proxy/add-cluster.png)

    Se você adicionar uma conexão a mais de um cluster de proxy, dê a cada um nome diferente. Caso contrário, eles todos terá o mesmo nome no painel esquerdo.

1. Depois que a conexão é estabelecida, o cluster de IA LA aparecerá no painel esquerdo, com o cluster ADX nativo. 

    ![Clusters do log Analytics e o Data Explorer do Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Executar consultas

Você pode usar o Gerenciador de Kusto, web ADX Explorer, Jupyter Kqlmagic, ou a API REST para consultar os clusters de proxy. 

> [!TIP]
> * Nome do banco de dados deve ter o mesmo nome que o recurso especificado no cluster do proxy. Nomes diferenciam maiusculas de minúsculas.
> * Em consultas de cluster entre, certifique-se de que o [nomeação dos aplicativos e espaços de trabalho](#application-insights-app-and-log-analytics-workspace-names) está correto.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Consultar o cluster Data Explorer do Azure nativo 

Executar consultas em seu cluster do Azure Data Explorer (como *StormEvents* na tabela *ajudar* cluster). Ao executar a consulta, verificar se o seu cluster nativo do Gerenciador de dados do Azure está selecionado no painel esquerdo.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Tabela de StormEvents de consulta](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Consultar seu cluster LA ou AI

Quando você executa consultas em seu cluster LA ou AL, verifique se seu cluster LA ou AI é selecionada no painel esquerdo. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Espaço de trabalho de consulta LA](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Consultar seu cluster LA ou AI do proxy ADX  

Quando você executa consultas em seu cluster LA ou AI do proxy, verifique se que seu cluster ADX nativo está selecionado no painel esquerdo. O exemplo a seguir demonstra uma consulta do espaço de trabalho LA usando o cluster ADX nativo

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Consulta do proxy de Gerenciador de dados do Azure](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Consulta de cluster LA ou AI e o cluster ADX entre do proxy ADX 

Quando você executa consultas cruzadas de cluster do proxy, verifique se que seu cluster ADX nativo está selecionado no painel esquerdo. Os exemplos a seguir demonstram combinando ADX tabelas do cluster (usando `union`) com o espaço de trabalho de LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Consulta entre do proxy de Gerenciador de dados do Azure](media/adx-proxy/cross-query-adx-proxy.png)

Usando o [ `join` operador](/azure/kusto/query/joinoperator), em vez de união, pode exigir uma dica para executá-lo em um cluster do Gerenciador de dados do Azure nativo (e não no proxy). 

## <a name="additional-syntax-examples"></a>Exemplos de sintaxe adicional

As opções de sintaxe a seguir estão disponíveis ao chamar os clusters do Application Insights (AI) ou o Log Analytics (LA):

|Descrição da sintaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Banco de dados dentro de um cluster que contém apenas o recurso definido nesta assinatura (**recomendada para consultas de cluster entre**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster que contém todos os aplicativos/os espaços de trabalho nesta assinatura    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster que contém todos os aplicativos/os espaços de trabalho na assinatura e os membros desse grupo de recursos    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster que contém apenas o recurso definido nesta assinatura      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Aplicativo do Application Insights e nomes de espaço de trabalho do Log Analytics

* Se os nomes contiverem caracteres especiais, eles são substituídos por codificação no nome do cluster de proxy de URL. 
* Se os nomes incluírem caracteres que não atendem [as regras de nome de identificador KQL](/azure/kusto/query/schema-entities/entity-names), são substituídos pelo traço **-** caractere.

## <a name="next-steps"></a>Próximas etapas

[Gravar consultas](write-queries.md)
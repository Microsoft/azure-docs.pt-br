---
title: Consulta entre recursos do Azure Data Explorer usando Azure Monitor
description: Use Azure Monitor para executar consultas entre produtos entre os Data Explorer do Azure, os espaços de trabalho do Log Analytics e os aplicativos Application Insights clássicos no Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: cb586d15e762f88620fe0c91152af41b3f607d74
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674422"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Consulta entre recursos do Azure Data Explorer usando Azure Monitor
O Azure Monitor dá suporte a consultas entre serviços entre o Azure Data Explorer, o [Application insights](/azure/azure-monitor/app/app-insights-overview)e o [log Analytics](/azure/azure-monitor/platform/data-platform-logs). Em seguida, você pode consultar o cluster de Data Explorer do Azure usando ferramentas de Log Analytics/Application Insights e consultá-lo em uma consulta entre serviços. O artigo mostra como fazer uma consulta entre serviços.

O diagrama a seguir mostra o Azure Monitor fluxo entre serviços:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagrama que mostra o fluxo de consultas entre um usuário, Azure Monitor, um proxy e Data Explorer do Azure.":::

>[!NOTE]
> Azure Monitor consulta entre serviços está em visualização privada. Allowlisting é obrigatório. Entre em contato com a [equipe de serviço](mailto:ADXProxy@microsoft.com) com perguntas.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Consulta cruzada de seus Log Analytics ou recursos de Application Insights e Data Explorer do Azure

Você pode executar consultas de recurso cruzado usando ferramentas de cliente que dão suporte a consultas Kusto. Exemplos dessas ferramentas incluem a interface do usuário da Web do Log Analytics, pastas de trabalho, PowerShell e a API REST.

Insira o identificador para um cluster de Data Explorer do Azure em uma consulta dentro do `adx` padrão, seguido pelo nome do banco de dados e pela tabela.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Captura de tela que mostra um exemplo de uma consulta entre serviços.":::

> [!NOTE]
>* Nomes de bancos de dados diferenciam maiúsculas de minúsculas
>* Não há suporte para a consulta entre recursos como um alerta.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Combinar tabelas de cluster Data Explorer do Azure com um espaço de trabalho Log Analytics

Use o `union` comando para combinar tabelas de cluster com um espaço de trabalho log Analytics.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Captura de tela que mostra um exemplo de consulta entre serviços com o comando Union.":::

> [!Tip]
> Formato abreviado é permitido: *ClusterName* / *InitialCatalog*. Por exemplo, `adx('help/Samples')` é traduzido para `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Unir dados de um cluster do Azure Data Explorer em um locatário com um recurso do Azure Monitor em outro locatário

Não há suporte para consultas entre locatários entre os serviços. Você está conectado a um único locatário para executar a consulta que abrange os dois recursos.

Se o recurso de Data Explorer do Azure estiver no locatário A e o espaço de trabalho Log Analytics estiver no locatário B, use um dos seguintes métodos:

*  O Azure Data Explorer permite que você adicione funções para entidades de segurança em locatários diferentes. Adicione sua ID de usuário no locatário B como um usuário autorizado no cluster Data Explorer do Azure. Valide se a propriedade [TrustedExternalTenant](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster) no cluster data Explorer do Azure contém o locatário b. Execute a consulta cruzada totalmente no locatário b.
*  Use [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) para projetar o recurso Azure monitor no locatário a.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Conectar-se aos clusters do Azure Data Explorer de diferentes locatários

O Kusto Explorer entra automaticamente no locatário ao qual a conta de usuário pertence originalmente. Para acessar recursos em outros locatários com a mesma conta de usuário, você deve especificar explicitamente `TenantId` na cadeia de conexão:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Próximas etapas
* [Gravar consultas](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Consultar dados em Azure Monitor usando o Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Executar consultas entre logs de recursos no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)

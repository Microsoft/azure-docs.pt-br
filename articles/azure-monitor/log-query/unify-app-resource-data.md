---
title: Unificar vários recursos do Application Insights do Azure Monitor | Microsoft Docs
description: Este artigo fornece detalhes sobre como usar uma função nos Logs do Azure Monitor para consultar vários recursos do Application Insights e visualizar esses dados.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 360578a36b92711c55b1fc65befa1b3df7927aad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330886"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unificar vários recursos do Application Insights do Azure Monitor 
Este artigo descreve como consultar e exibir todos os seus Application Insights dados de log em um único local, mesmo quando eles estão em assinaturas diferentes do Azure, como uma substituição para a reprovação da Conector do Application Insights. O número de recursos de Application Insights que você pode incluir em uma única consulta é limitado a 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Abordagem recomendada para consultar vários recursos do Application Insights 
Listar vários recursos do Application Insights em uma consulta pode ser complicado e difícil de manter. Em vez disso, você pode aproveitar a função para separar a lógica da consulta do escopo dos aplicativos.  

Este exemplo demonstra como você pode monitorar vários recursos do Application Insights e visualizar a contagem de solicitações com falha pelo nome do aplicativo.

Crie uma função usando o operador de união com a lista de aplicativos e, em seguida, salve a consulta em seu workspace como uma função com o alias *applicationsScoping*. 

Você pode modificar os aplicativos listados a qualquer momento navegando até o Gerenciador de consultas no seu workspace e selecionando a função para editar e salvar, ou então, usando o cmdlet `SavedSearch` do PowerShell. 

>[!NOTE]
>Esse método não pode ser usado com alertas de log porque a validação de acesso dos recursos de regra de alerta, incluindo espaços de trabalho e aplicativos, é executada no momento da criação do alerta. Não há suporte para a adição de novos recursos à função após a criação do alerta. Se preferir usar a função para o escopo de recursos em alertas de log, você precisará editar a regra de alerta no portal ou com um modelo do Resource Manager para atualizar os recursos com escopo. Como alternativa, você pode incluir a lista de recursos na consulta de alerta de log.

O comando `withsource= SourceApp` adiciona aos resultados uma coluna que designa o aplicativo que enviou o log. O operador Parse é opcional neste exemplo e usa para extrair o nome do aplicativo da propriedade SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Agora você pode usar a função applicationsScoping na consulta entre recursos:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A consulta usa o esquema do Application Insights, embora ela seja executada no workspace, pois a função applicationsScoping retorna a estrutura de dados do Application Insights. O alias de função retorna a união das solicitações de todos os aplicativos definidos. A consulta então filtra solicitações com falha e visualiza as tendências por aplicativo.

![Exemplo de resultados de consulta cruzada](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>As [consultas entre recursos](./cross-workspace-query.md) em alertas de log só têm suporte na [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)atual. Se você estiver usando a API de alertas de Log Analytics herdados, será necessário [alternar para a API atual](../platform/alerts-log-api-switch.md). [Consulte modelos de exemplo](../platform/alerts-log-create-templates.md).

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Diferenças de esquema de espaço de trabalho do Application Insights e do Log Analytics
A tabela a seguir mostra as diferenças de esquema entre o Log Analytics e o Application Insights.  

| Propriedades do espaço de trabalho do Log Analytics| Propriedades de recurso do Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | mensagem |
| AvailabilityRunLocation | local |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Navegador | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computador | cloud_RoleInstance | 
| País | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | mensagem | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| Sistema operacional | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | sucesso | 
| ResponseCode | resultCode | 
| Função | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Próximas etapas

Use a [Pesquisa de Logs](./log-query-overview.md) para exibir informações detalhadas dos aplicativos do Application Insights.


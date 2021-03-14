---
title: Auditar consultas e atividades do Azure Sentinel | Microsoft Docs
description: Este artigo descreve como auditar consultas e atividades executadas no Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a8ea32d84da521c8a1af926c6cb5e26bc2738de2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054441"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Auditar consultas e atividades do Azure Sentinel

Este artigo descreve como você pode exibir dados de auditoria para execução de consultas e atividades executadas em seu espaço de trabalho do Azure Sentinel, como para requisitos de conformidade internos e externos em seu espaço de trabalho de operações de segurança (SOC).

O Azure Sentinel fornece acesso a:

- A tabela **AzureActivity** , que fornece detalhes sobre todas as ações executadas no Azure Sentinel, como a edição de regras de alerta. A tabela **AzureActivity** não registra dados de consulta específicos. Para obter mais informações, consulte [auditoria com logs de atividades do Azure](#auditing-with-azure-activity-logs).

- A tabela **LAQueryLogs** , que fornece detalhes sobre as consultas executadas no log Analytics, incluindo consultas executadas do Azure Sentinel. Para obter mais informações, consulte [Auditing with LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> Além das consultas manuais descritas neste artigo, o Azure Sentinel fornece uma pasta de trabalho interna para ajudá-lo a auditar as atividades em seu ambiente SOC.
>
> Na área pastas de **trabalho** do Azure Sentinel, procure a pasta de trabalho de **auditoria do espaço** de trabalho.



## <a name="auditing-with-azure-activity-logs"></a>Auditoria com logs de atividade do Azure

Os logs de auditoria do Sentinela do Azure são mantidos nos [logs de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md), em que a tabela **AzureActivity** inclui todas as ações executadas em seu espaço de trabalho do Azure Sentinel.

Você pode usar a tabela **AzureActivity** ao auditar a atividade no ambiente do SOC com o Azure Sentinel.

**Para consultar a tabela AzureActivity**:

1. Conecte a fonte de dados da [atividade do Azure](connect-azure-activity.md) para iniciar o streaming de eventos de auditoria em uma nova tabela na tela de **logs** chamada AzureActivity.

1. Em seguida, consulte os dados usando KQL, como faria com qualquer outra tabela.

    A tabela **AzureActivity** inclui dados de vários serviços, incluindo o Azure Sentinel. Para filtrar somente os dados do Azure Sentinel, inicie a consulta com o seguinte código:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Por exemplo, para descobrir quem foi o último usuário para editar uma regra de análise específica, use a seguinte consulta (substituindo `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` pela ID da regra que você deseja verificar):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Adicione mais parâmetros à sua consulta para explorar ainda mais a tabela **AzureActivities** , dependendo do que você precisa relatar. As seções a seguir fornecem outras consultas de exemplo a serem usadas durante a auditoria com os dados da tabela **AzureActivity** . 

Para obter mais informações, consulte [dados do Azure Sentinel incluídos nos logs de atividades do Azure](#azure-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Localizar todas as ações executadas por um usuário específico nas últimas 24 horas

A consulta de tabela **AzureActivity** a seguir lista todas as ações executadas por um usuário específico do Azure ad nas últimas 24 horas.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Localizar todas as operações de exclusão

A consulta de tabela **AzureActivity** a seguir lista todas as operações de exclusão executadas no seu espaço de trabalho do Azure Sentinel.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Dados do Azure Sentinel incluídos nos logs de atividades do Azure
 
Os logs de auditoria do Sentinela do Azure são mantidos nos [logs de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md)e incluem os seguintes tipos de informações:

|Operação  |Tipos de informações  |
|---------|---------|
|**Criado**     |Regras de alerta <br> Comentários de caso <br>Comentários do incidente <br>Pesquisas salvas<br>Watchlists    <br>Pastas de trabalho     |
|**Excluída**     |Regras de alerta <br>Indicadores <br>Conectores de dados <br>Incidentes <br>Pesquisas salvas <br>Configurações <br>Relatórios de inteligência contra ameaças <br>Watchlists      <br>Pastas de trabalho <br>Fluxo de trabalho  |
|**Updated**     |  Regras de alerta<br>Indicadores <br> Casos <br> Conectores de dados <br>Incidentes <br>Comentários do incidente <br>Relatórios de inteligência contra ameaças <br> Pastas de trabalho <br>Fluxo de trabalho       |
|     |         |

Você também pode usar os logs de atividades do Azure para verificar autorizações e licenças de usuário. 

Por exemplo, a tabela a seguir lista as operações selecionadas encontradas nos logs de atividades do Azure com o recurso específico do qual os dados de log são extraídos.

|Nome da operação|    Tipo de recurso|
|----|----|
|Criar ou atualizar pasta de trabalho  |Microsoft. insights/pastas de trabalho|
|Excluir pasta de trabalho    |Microsoft. insights/pastas de trabalho|
|Definir fluxo de trabalho   |Microsoft.Logic/workflows|
|Excluir fluxo de trabalho    |Microsoft.Logic/workflows|
|Criar pesquisa salva    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Excluir pesquisa salva    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Atualizar regras de alerta |Microsoft. SecurityInsights/alertRules|
|Excluir regras de alerta |Microsoft. SecurityInsights/alertRules|
|Atualizar ações de resposta da regra de alerta |Microsoft. SecurityInsights/alertRules/Actions|
|Excluir ações de resposta da regra de alerta |Microsoft. SecurityInsights/alertRules/Actions|
|Atualizar indicadores   |Microsoft. SecurityInsights/bookmarks|
|Excluir indicadores   |Microsoft. SecurityInsights/bookmarks|
|Atualizar casos   |Microsoft. SecurityInsights/casos|
|Atualizar investigação de caso  |Microsoft. SecurityInsights/casos/investigações|
|Criar comentários de caso   |Microsoft. SecurityInsights/casos/comentários|
|Atualizar conectores de dados |Microsoft. SecurityInsights/dataconnecters|
|Excluir conectores de dados |Microsoft. SecurityInsights/dataconnecters|
|Atualizar configurações    |Microsoft. SecurityInsights/Settings|
| | |

Para obter mais informações, consulte [esquema de eventos do log de atividades do Azure](/azure/azure-monitor/essentials/activity-log-schema).


## <a name="auditing-with-laquerylogs"></a>Auditoria com LAQueryLogs

A tabela **LAQueryLogs** fornece detalhes sobre as consultas de log executadas em log Analytics. Como Log Analytics é usado como armazenamento de dados subjacente do Sentinela do Azure, você pode configurar seu sistema para coletar dados de LAQueryLogs em seu espaço de trabalho do Azure Sentinel.

Os dados do LAQueryLogs incluem informações como:

- Quando as consultas foram executadas
- Quem executou consultas em Log Analytics
- Qual ferramenta foi usada para executar consultas em Log Analytics, como o Azure Sentinel
- Os próprios textos de consulta
- Dados de desempenho em cada execução de consulta

> [!NOTE]
> - A tabela **LAQueryLogs** apenas inclui consultas que foram executadas na folha logs do Azure Sentinel. Ele não inclui as consultas executadas por regras de análise agendadas, usando o **grafo de investigação** ou a página de **busca** do Azure Sentinel.
> - Pode haver um pequeno atraso entre o momento em que uma consulta é executada e os dados são preenchidos na tabela **LAQueryLogs** . É recomendável aguardar cerca de 5 minutos para consultar a tabela **LAQueryLogs** para obter dados de auditoria.
>


**Para consultar a tabela LAQueryLogs**:

1. A tabela **LAQueryLogs** não está habilitada por padrão em seu espaço de trabalho log Analytics. Para usar dados do **LAQueryLogs** ao auditar no Azure Sentinel, primeiro habilite o **LAQueryLogs** na área de **configurações de diagnóstico** do espaço de trabalho log Analytics.

    Para obter mais informações, consulte [auditar consultas em logs de Azure monitor](/azure/azure-monitor/logs/query-audit).


1. Em seguida, consulte os dados usando KQL, como faria com qualquer outra tabela.

    Por exemplo, a consulta a seguir mostra quantas consultas foram executadas na última semana, por dia:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

As seções a seguir mostram mais consultas de exemplo a serem executadas na tabela **LAQueryLogs** ao auditar atividades em seu ambiente do SOC usando o Azure Sentinel.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>O número de consultas executadas onde a resposta não era "OK"

A consulta de tabela **LAQueryLogs** a seguir mostra o número de consultas executadas, em que qualquer outra coisa que não seja uma resposta HTTP de **200 OK** foi recebida. Por exemplo, esse número incluirá consultas que falharam na execução.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Mostrar usuários para consultas com uso intensivo de CPU

A consulta de tabela **LAQueryLogs** a seguir lista os usuários que executaram mais consultas com uso intensivo de CPU, com base na CPU usada e no comprimento do tempo de consulta.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Mostrar usuários que executaram a maioria das consultas na última semana

A consulta de tabela **LAQueryLogs** a seguir lista os usuários que executaram a maioria das consultas na última semana.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Configurando alertas para atividades do Azure Sentinel

Talvez você queira usar os recursos de auditoria do Azure Sentinel para criar alertas proativos.

Por exemplo, se você tiver tabelas confidenciais em seu espaço de trabalho do Azure Sentinel, use a consulta a seguir para notificá-lo toda vez que essas tabelas forem consultadas:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>Próximas etapas

No Azure Sentinel, use a pasta de trabalho de **auditoria do espaço** de trabalho para auditar as atividades em seu ambiente do SOC.

Para obter mais informações, consulte [tutorial: Visualizar e monitorar seus dados](tutorial-monitor-your-data.md).

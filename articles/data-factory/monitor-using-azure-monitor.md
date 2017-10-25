---
title: Monitorar data factories usando o Azure Monitor | Microsoft Docs
description: "Saiba como usar o Azure Monitor para monitorar os pipelines do Data Factory, permitindo logs de diagnóstico com informações do Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: a130907fe6cf2ae6498502644949290bc838f239
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Monitorar data factories usando o Azure Monitor  
Os aplicativos em nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça ativo e em execução em um estado íntegro. Ele também ajuda a afastar os problemas potenciais ou solucionar problemas antigos. Além disso, você pode usar os dados de monitoramento para obter mais informações sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que normalmente exigiriam intervenção manual.

O Azure Monitor fornece logs e métricas de infraestrutura de nível básico para a maioria dos serviços do Microsoft Azure. Para obter detalhes, consulte [Visão geral do monitoramento](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Os logs de Diagnóstico do Azure são logs emitidos por um recurso que fornece dados avançados e frequentes sobre a operação do recurso. O Data Factory gera logs de diagnóstico no Azure Monitor. 

## <a name="diagnostic-logs"></a>Logs de diagnóstico

* Salve-os em uma **Conta de Armazenamento** para inspeção manual ou de auditoria. Você pode especificar o tempo (em dias) de retenção usando as configurações de diagnóstico.
* Transmita-os para os **Hubs de Eventos** para ingestão por um serviço de terceiros ou uma solução de análises personalizadas, como o PowerBI.
* Analise-os com o **Log Analytics do OMS (Operations Management Suite)**

Você pode usar uma conta de armazenamento ou um namespace de hub de eventos que não esteja na mesma assinatura como o recurso que está emitindo logs. O usuário que define a configuração deve ter o devido acesso RBAC (controle de acesso baseado em função) para ambas as assinaturas.

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

### <a name="diagnostic-settings"></a>Configurações de Diagnóstico
Os Logs de Diagnóstico para os recursos de não computação são configurados usando as configurações de diagnóstico. Configurações de diagnóstico para um controle de recursos:

* Para onde os logs de diagnóstico são enviados (Conta de Armazenamento, Hubs de Eventos e/ou Log Analytics do OMS).
* Quais categorias de log são enviadas.
* Quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento
* Uma retenção de zero dias significa que os registros serão mantidos indefinidamente. O valor pode ser qualquer quantidade de dias, entre 1 e 2147483647.
* Se as políticas de retenção são definidas, mas o armazenamento dos logs em uma conta de armazenamento está desabilitado (por exemplo, se apenas as opções Hubs de Eventos ou OMS estão selecionadas), as políticas de retenção não têm nenhum efeito.
* As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Habilitar os logs de diagnóstico usando APIs REST

Criar ou atualizar uma configuração de diagnóstico na API REST do Azure Monitor

**Solicitação**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Cabeçalhos**
* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` pela ID de recurso do recurso para o qual você deseja editar as configurações de diagnóstico. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/resource-group-portal.md).
* Defina o cabeçalho `Content-Type` como `application/json`.
* Defina o cabeçalho de autorização para um token Web JSON obtido do Azure Active Directory. Para mais informações, consulte [Autenticação de solicitações](../active-directory/develop/active-directory-authentication-scenarios.md).

**Corpo**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
} 
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| storageAccountId |Cadeia de caracteres | A ID de recurso da conta de armazenamento a qual você gostaria de enviar os logs de diagnóstico |
| serviceBusRuleId |Cadeia de caracteres | A ID da regra de barramento de serviço para o namespace do barramento de serviço no qual você gostaria que os hubs de eventos fossem criados para streaming dos Logs de Diagnóstico. A ID da regra está no formato: "{ID de recurso do barramento de serviço}/authorizationrules/ {nome da chave}".|
| workspaceId | Tipo complexo | Matriz de intervalo de agregação de métrica e suas políticas de retenção. No momento, essa propriedade está vazia. |
|Métricas| Valores de parâmetro da execução do pipeline a serem passados para o pipeline invocado| Um parâmetro de mapeamento do objeto JSON nomeado para valores de argumento | 
| logs| Tipo complexo| Nome de uma categoria de Log de Diagnóstico para um tipo de recurso. Para obter a lista de categorias de Log de Diagnóstico para um recurso, primeiramente execute uma operação de configurações de diagnóstico GET. |
| categoria| Cadeia de caracteres| Matriz de categorias de log e suas políticas de retenção |
| timeGrain | Cadeia de caracteres | A granularidade das métricas que são capturadas no formato de duração ISO 8601. Deve ser PT1M (um minuto)|
| Habilitado| Booliano | Especifica se a coleção da métrica ou da categoria de log está habilitada para este recurso|
| retentionPolicy| Tipo complexo| Descreve a política de retenção para uma categoria de métrica ou de log. Usado apenas para a opção de conta de armazenamento.|
| dias| int| O número de dias para retenção das métricas ou logs. Um valor de 0 retém os logs indefinidamente. Usado apenas para a opção de conta de armazenamento. |

**Resposta**

200 OK


```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Obtenha informações sobre a configuração de diagnóstico na API REST do Azure Monitor

**Solicitação**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Cabeçalhos**
* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` pela ID de recurso do recurso para o qual você deseja editar as configurações de diagnóstico. Para obter mais informações, consulte Usando os grupos de recursos para gerenciar seus recursos do Azure.
* Defina o cabeçalho `Content-Type` como `application/json`.
* Defina o cabeçalho de autorização para um token Web JSON obtido do Azure Active Directory. Para mais informações, consulte Autenticação de solicitações.

**Resposta**

200 OK

```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/0ee78edb-a0ad-456c-a0a2-901bf542c102/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Mais informações aqui] (https://msdn.microsoft.com/en-us/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>Esquema de eventos e logs

### <a name="activity-run-logs-attributes"></a>Atributos de logs de execução de atividade

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties:" 
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propriedade | Tipo | Descrição | Exemplo |
| --- | --- | --- | --- |
| Nível |Cadeia de caracteres | Nível dos logs de diagnóstico. O nível 4 sempre é o caso para logs de execução de atividade. | `4`  |
| correlationId |Cadeia de caracteres | ID exclusiva para acompanhar uma solicitação específica ponta a ponta | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| tempo real | Cadeia de caracteres | Hora do evento no período de tempo, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Cadeia de caracteres| ID da execução de atividade | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Cadeia de caracteres| ID da execução de pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia de caracteres | ID de recursos associado para o recurso de data factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| Cadeia de caracteres | Categoria dos Logs de Diagnóstico. Defina essa propriedade para "ActivityRuns" | `ActivityRuns` |
|level| Cadeia de caracteres | Nível dos logs de diagnóstico. Defina essa propriedade para "Informational" | `Informational` |
|operationName| Cadeia de caracteres |Nome da atividade com status. Se o status for a pulsação de início, será `MyActivity -`. Se o status for a pulsação final, será `MyActivity - Succeeded` com status final | `MyActivity - Succeeded` |
|pipelineName| Cadeia de caracteres | Nome do pipeline | `MyPipeline` |
|activityName| Cadeia de caracteres | Nome da atividade | `MyActivity` |
|iniciar| Cadeia de caracteres | Início da execução de atividade no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Cadeia de caracteres | Fim da execução de atividade no período de tempo, formato UTC. Se a atividade não foi finalizada ainda (log de diagnóstico para uma atividade que está sendo iniciada), um valor padrão de `1601-01-01T00:00:00Z` será definido.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Atributos de logs de execução de pipeline

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties": 
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propriedade | Tipo | Descrição | Exemplo |
| --- | --- | --- | --- |
| Nível |Cadeia de caracteres | Nível dos logs de diagnóstico. O nível 4 é o caso para logs de execução de atividade. | `4`  |
| correlationId |Cadeia de caracteres | ID exclusiva para acompanhar uma solicitação específica ponta a ponta | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| tempo real | Cadeia de caracteres | Hora do evento no período de tempo, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Cadeia de caracteres| ID da execução de pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia de caracteres | ID de recursos associado para o recurso de data factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| Cadeia de caracteres | Categoria dos Logs de Diagnóstico. Defina essa propriedade para "PipelineRuns" | `PipelineRuns` |
|level| Cadeia de caracteres | Nível dos logs de diagnóstico. Defina essa propriedade para "Informational" | `Informational` |
|operationName| Cadeia de caracteres |Nome do pipeline com status. "Pipeline – bem-sucedido" com status final quando a execução do pipeline é concluída| `MyPipeline - Succeeded` |
|pipelineName| Cadeia de caracteres | Nome do pipeline | `MyPipeline` |
|iniciar| Cadeia de caracteres | Início da execução de atividade no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Cadeia de caracteres | Fim das execuções de atividade no período de tempo, formato UTC. Se a atividade não foi finalizada ainda (log de diagnóstico para uma atividade que está sendo iniciada), um valor padrão de `1601-01-01T00:00:00Z` será definido.  | `2017-06-26T20:55:29.5007959Z` |
|status| Cadeia de caracteres | Status final da execução do pipeline (Bem-sucedido ou Falha) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Atributos de logs de execução do gatilho

```json
{ 
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
} 

```

| Propriedade | Tipo | Descrição | Exemplo |
| --- | --- | --- | --- |
| Nível |Cadeia de caracteres | Nível dos logs de diagnóstico. Defina o nível 4 para logs de execução da atividade. | `4`  |
| correlationId |Cadeia de caracteres | ID exclusiva para acompanhar uma solicitação específica ponta a ponta | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| tempo real | Cadeia de caracteres | Hora do evento no período de tempo, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Cadeia de caracteres| ID da execução do gatilho | `08587023010602533858661257311` |
|resourceId| Cadeia de caracteres | ID de recursos associado para o recurso de data factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| Cadeia de caracteres | Categoria dos Logs de Diagnóstico. Defina essa propriedade para "PipelineRuns" | `PipelineRuns` |
|level| Cadeia de caracteres | Nível dos logs de diagnóstico. Defina essa propriedade para "Informational" | `Informational` |
|operationName| Cadeia de caracteres |Nome do gatilho com status final se ele foi disparado com êxito. "MyTrigger – Bem-sucedido" se a pulsação foi bem-sucedida| `MyTrigger - Succeeded` |
|triggerName| Cadeia de caracteres | Nome do gatilho | `MyTrigger` |
|triggerType| Cadeia de caracteres | Tipo de gatilho (gatilho Manual ou gatilho de Agendamento) | `ScheduleTrigger` |
|triggerEvent| Cadeia de caracteres | Evento do gatilho | `ScheduleTime - 2017-07-06T01:50:25Z` |
|iniciar| Cadeia de caracteres | Início do acionamento do gatilho no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| Cadeia de caracteres | Status final se o gatilho foi acionado com êxito (Bem-sucedido ou Falha) | `Succeeded`|

### <a name="metrics"></a>Métricas

O Azure Monitor permite consumir a telemetria para ter visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. Os tipos de dados de telemetria do Azure mais importantes são as métricas (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir essas métricas para monitorar e solucionar problemas.

O ADFV2 emite as seguintes métricas

| **Métrica**           | **Nome de exibição da métrica**         | **Unidade** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Métricas de execução do pipeline bem-sucedido | Contagem    | Total                | Execuções totais dos pipelines bem-sucedidas em uma janela de um minuto |
| PipelineFailedRuns   | Métricas de execução do pipeline com falha    | Contagem    | Total                | Execuções totais dos pipelines que falharam em uma janela de um minuto    |
| ActiviySucceededRuns | Métricas de execução de atividades bem-sucedidas | Contagem    | Total                | Execuções totais da atividade bem-sucedidas em uma janela de um minuto  |
| ActivityFailedRuns   | Métricas de execução de atividades com falha    | Contagem    | Total                | Execuções totais da atividade com falha em uma janela de um minuto     |
| TriggerSucceededRuns | Métricas de execuções do gatilho bem-sucedidas  | Contagem    | Total                | Execuções totais do gatilho bem-sucedidas em uma janela de um minuto   |
| TriggerFailedRuns    | Métricas de execuções do gatilho com falha     | Contagem    | Total                | Execuções totais do gatilho com falha em uma janela de um minuto      |

Para acessar as métricas, siga as instruções no artigo – https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics 

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md) para saber mais sobre o monitoramento e o gerenciamento de pipelines ao executar. 
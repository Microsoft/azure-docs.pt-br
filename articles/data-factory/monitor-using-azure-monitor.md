---
title: Monitorar data factories usando o Azure Monitor | Microsoft Docs
description: Saiba como usar o Azure Monitor para monitorar os pipelines do Data Factory, permitindo logs de diagnóstico com informações do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: 6bad74d33f5d50bb7a35de69927bf97daad07798
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326860"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Monitorar e alertar data factories usando o Azure Monitor
Os aplicativos em nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça ativo e em execução em um estado íntegro. Ele também ajuda a afastar os problemas potenciais ou solucionar problemas antigos. Além disso, você pode usar os dados de monitoramento para obter mais informações sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que normalmente exigiriam intervenção manual.

O Azure Monitor fornece logs e métricas de infraestrutura de nível básico para a maioria dos serviços do Microsoft Azure. Para obter detalhes, consulte [Visão geral do monitoramento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Os logs de Diagnóstico do Azure são logs emitidos por um recurso que fornece dados avançados e frequentes sobre a operação do recurso. O Data Factory gera logs de diagnóstico no Azure Monitor.

## <a name="persist-data-factory-data"></a>Persistir dados do Data Factory
O Data Factory armazena apenas os dados executados no pipeline por 45 dias. Se você quiser persistir dados de execução de pipeline por mais de 45 dias, usando o Azure Monitor, poderá rotear os logs de diagnóstico para análise e mantê-los em uma conta de armazenamento para ter informações de fábrica durante o período de sua escolha.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

* Salve-os em uma **Conta de Armazenamento** para inspeção manual ou de auditoria. Você pode especificar o tempo (em dias) de retenção usando as configurações de diagnóstico.
* Transmita-os para os **Hubs de Eventos** para ingestão por um serviço de terceiros ou uma solução de análises personalizadas, como o Power BI.
* Analise-os com o **Log Analytics**

Você pode usar uma conta de armazenamento ou um namespace de hub de eventos que não esteja na mesma assinatura como o recurso que está emitindo logs. O usuário que define a configuração deve ter o devido acesso RBAC (controle de acesso baseado em função) para ambas as assinaturas.

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

### <a name="diagnostic-settings"></a>Configurações de Diagnóstico
Os Logs de Diagnóstico para os recursos de não computação são configurados usando as configurações de diagnóstico. Configurações de diagnóstico para um controle de recursos:

* Onde os logs de diagnóstico são enviados (conta de armazenamento, hubs de eventos ou logs de Azure Monitor).
* Quais categorias de log são enviadas.
* Quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento.
* Uma retenção de zero dias significa que os logs serão mantidos indefinidamente. O valor pode ser qualquer quantidade de dias, entre 1 e 2147483647.
* Se as políticas de retenção forem definidas, mas o armazenamento de logs em uma conta de armazenamento estiver desabilitado (por exemplo, somente as opções hubs de eventos ou logs de Azure Monitor são selecionadas), as políticas de retenção não terão nenhum efeito.
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
* Substitua `{resource-id}` pela ID de recurso do recurso para o qual você deseja editar as configurações de diagnóstico. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Defina o cabeçalho `Content-Type` como `application/json`.
* Defina o cabeçalho de autorização para um token Web JSON obtido do Azure Active Directory. Para mais informações, consulte [Autenticação de solicitações](../active-directory/develop/authentication-scenarios.md).

**Corpo**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
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

| Propriedade | Tipo | DESCRIÇÃO |
| --- | --- | --- |
| storageAccountId |Cadeia de caracteres | A ID de recurso da conta de armazenamento a qual você gostaria de enviar os logs de diagnóstico |
| serviceBusRuleId |Cadeia de caracteres | A ID da regra de barramento de serviço para o namespace do barramento de serviço no qual você gostaria que os hubs de eventos fossem criados para streaming dos Logs de Diagnóstico. A ID da regra está no formato: "{ID de recurso do barramento de serviço}/authorizationrules/ {nome da chave}".|
| workspaceId | Tipo complexo | Matriz de intervalo de agregação de métrica e suas políticas de retenção. No momento, essa propriedade está vazia. |
|metrics| Valores de parâmetro da execução do pipeline a serem passados para o pipeline invocado| Um parâmetro de mapeamento do objeto JSON nomeado para valores de argumento |
| logs| Tipo complexo| Nome de uma categoria de Log de Diagnóstico para um tipo de recurso. Para obter a lista de categorias de Log de Diagnóstico para um recurso, primeiramente execute uma operação de configurações de diagnóstico GET. |
| category| Cadeia de caracteres| Matriz de categorias de log e suas políticas de retenção |
| timeGrain | Cadeia de caracteres | A granularidade das métricas que são capturadas no formato de duração ISO 8601. Deve ser PT1M (um minuto)|
| enabled| Boolean | Especifica se a coleção da métrica ou da categoria de log está habilitada para este recurso|
| retentionPolicy| Tipo complexo| Descreve a política de retenção para uma categoria de métrica ou de log. Usado apenas para a opção de conta de armazenamento.|
| days| int| O número de dias para retenção das métricas ou logs. Um valor de 0 retém os logs indefinidamente. Usado apenas para a opção de conta de armazenamento. |

**Resposta**

200 OK


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
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
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
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
[Mais informações aqui](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Esquema de eventos e logs

### <a name="azure-monitor-schema"></a>Esquema de Azure Monitor

#### <a name="activity-run-logs-attributes"></a>Atributos de logs de execução de atividade

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
   "properties":
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

| Propriedade | Tipo | DESCRIÇÃO | Exemplo |
| --- | --- | --- | --- |
| Nível |Cadeia de caracteres | Nível dos logs de diagnóstico. O nível 4 sempre é o caso para logs de execução de atividade. | `4`  |
| correlationId |Cadeia de caracteres | ID exclusiva para acompanhar uma solicitação específica ponta a ponta | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Cadeia de caracteres | Hora do evento em TimeSpan, formato UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Cadeia de caracteres| ID da execução de atividade | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Cadeia de caracteres| ID da execução de pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia de caracteres | ID de recursos associado para o recurso de data factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Cadeia de caracteres | Categoria dos Logs de Diagnóstico. Defina essa propriedade para "ActivityRuns" | `ActivityRuns` |
|level| Cadeia de caracteres | Nível dos logs de diagnóstico. Defina essa propriedade para "Informational" | `Informational` |
|operationName| Cadeia de caracteres |Nome da atividade com status. Se o status for a pulsação de início, será `MyActivity -`. Se o status for a pulsação final, será `MyActivity - Succeeded` com status final | `MyActivity - Succeeded` |
|pipelineName| Cadeia de caracteres | Nome do pipeline | `MyPipeline` |
|activityName| Cadeia de caracteres | Nome da atividade | `MyActivity` |
|start| Cadeia de caracteres | Início da execução de atividade no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Cadeia de caracteres | Fim da execução de atividade no período de tempo, formato UTC. Se a atividade não foi finalizada ainda (log de diagnóstico para uma atividade que está sendo iniciada), um valor padrão de `1601-01-01T00:00:00Z` será definido.  | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-logs-attributes"></a>Atributos de logs de execução de pipeline

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

| Propriedade | Tipo | DESCRIÇÃO | Exemplo |
| --- | --- | --- | --- |
| Nível |Cadeia de caracteres | Nível dos logs de diagnóstico. O nível 4 é o caso para logs de execução de atividade. | `4`  |
| correlationId |Cadeia de caracteres | ID exclusiva para acompanhar uma solicitação específica ponta a ponta | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Cadeia de caracteres | Hora do evento em TimeSpan, formato UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Cadeia de caracteres| ID da execução de pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia de caracteres | ID de recursos associado para o recurso de data factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Cadeia de caracteres | Categoria dos Logs de Diagnóstico. Defina essa propriedade para "PipelineRuns" | `PipelineRuns` |
|level| Cadeia de caracteres | Nível dos logs de diagnóstico. Defina essa propriedade para "Informational" | `Informational` |
|operationName| Cadeia de caracteres |Nome do pipeline com status. "Pipeline – bem-sucedido" com status final quando a execução do pipeline é concluída| `MyPipeline - Succeeded` |
|pipelineName| Cadeia de caracteres | Nome do pipeline | `MyPipeline` |
|start| Cadeia de caracteres | Início da execução de atividade no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Cadeia de caracteres | Fim das execuções de atividade no período de tempo, formato UTC. Se a atividade não foi finalizada ainda (log de diagnóstico para uma atividade que está sendo iniciada), um valor padrão de `1601-01-01T00:00:00Z` será definido.  | `2017-06-26T20:55:29.5007959Z` |
|status| Cadeia de caracteres | Status final da execução do pipeline (Bem-sucedido ou Falha) | `Succeeded`|

#### <a name="trigger-run-logs-attributes"></a>Atributos de logs de execução do gatilho

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

| Propriedade | Tipo | DESCRIÇÃO | Exemplo |
| --- | --- | --- | --- |
| Nível |Cadeia de caracteres | Nível dos logs de diagnóstico. Defina o nível 4 para logs de execução da atividade. | `4`  |
| correlationId |Cadeia de caracteres | ID exclusiva para acompanhar uma solicitação específica ponta a ponta | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Cadeia de caracteres | Hora do evento em TimeSpan, formato UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Cadeia de caracteres| ID da execução do gatilho | `08587023010602533858661257311` |
|resourceId| Cadeia de caracteres | ID de recursos associado para o recurso de data factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Cadeia de caracteres | Categoria dos Logs de Diagnóstico. Defina essa propriedade para "PipelineRuns" | `PipelineRuns` |
|level| Cadeia de caracteres | Nível dos logs de diagnóstico. Defina essa propriedade para "Informational" | `Informational` |
|operationName| Cadeia de caracteres |Nome do gatilho com status final se ele foi disparado com êxito. "MyTrigger – Bem-sucedido" se a pulsação foi bem-sucedida| `MyTrigger - Succeeded` |
|triggerName| Cadeia de caracteres | Nome do gatilho | `MyTrigger` |
|triggerType| Cadeia de caracteres | Tipo de gatilho (gatilho Manual ou gatilho de Agendamento) | `ScheduleTrigger` |
|triggerEvent| Cadeia de caracteres | Evento do gatilho | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Cadeia de caracteres | Início do acionamento do gatilho no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| Cadeia de caracteres | Status final se o gatilho foi acionado com êxito (Bem-sucedido ou Falha) | `Succeeded`|

### <a name="log-analytics-schema"></a>Esquema de Log Analytics

Log Analytics herda o esquema do Azure Monitor com as seguintes exceções:

* A primeira letra em cada nome de coluna será capitalizada, por exemplo  , CorrelationId em Azure monitor será CorrelationId em log Analytics.
* O *nível* de coluna será Descartado.
* As *Propriedades* de coluna dinâmicas serão preservadas como o tipo de blob JSON dinâmico abaixo:

    | Azure Monitor coluna | Log Analytics coluna | Tipo |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dinâmico |
    | $. Properties. Anotações | Anotações | Dinâmico |
    | $. Properties. Entrada | Entrada | Dinâmico |
    | $. Properties. Der | Saída | Dinâmico |
    | $. Properties. Erro. errorCode | ErrorCode | int |
    | $. Properties. Erro. mensagem | ErrorMessage | cadeia de caracteres |
    | $. Properties. Ao | Erro | Dinâmico |
    | $. Properties. Predecessores | Predecessores | Dinâmico |
    | $. Properties. Parâmetro | parâmetros | Dinâmico |
    | $. Properties. Sistemaparameters | Sistemaparameters | Dinâmico |
    | $. Properties. Sinalizadores | Marcas | Dinâmico |
    
## <a name="metrics"></a>metrics

O Azure Monitor permite consumir a telemetria para ter visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. Os tipos de dados de telemetria do Azure mais importantes são as métricas (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir essas métricas para monitorar e solucionar problemas.

ADFV2 emite as seguintes métricas:

| **Métrica**           | **Nome de exibição da métrica**         | **Unidade** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Métricas de execução do pipeline bem-sucedido | Contagem    | Total                | Execuções totais dos pipelines bem-sucedidas em uma janela de um minuto |
| PipelineFailedRuns   | Métricas de execução do pipeline com falha    | Count    | Total                | Execuções totais dos pipelines que falharam em uma janela de um minuto    |
| ActivitySucceededRuns | Métricas de execução de atividades bem-sucedidas | Contagem    | Total                | Execuções totais da atividade bem-sucedidas em uma janela de um minuto  |
| ActivityFailedRuns   | Métricas de execução de atividades com falha    | Count    | Total                | Execuções totais da atividade com falha em uma janela de um minuto     |
| TriggerSucceededRuns | Métricas de execuções do gatilho bem-sucedidas  | Count    | Total                | Execuções totais do gatilho bem-sucedidas em uma janela de um minuto   |
| TriggerFailedRuns    | Métricas de execuções do gatilho com falha     | Count    | Total                | Execuções totais do gatilho com falha em uma janela de um minuto      |

Para acessar as métricas, conclua as instruções em [Azure monitor plataforma de dados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitorar métricas do Data Factory com o Azure Monitor

Use a integração do Azure Data Factory com o Azure Monitor para encaminhar dados ao Azure Monitor. Essa integração é útil nos seguintes cenários:

1.  Você quer escrever consultas complexas em um conjunto avançado de métricas publicadas pelo Data Factory no Azure Monitor. Você também pode criar alertas personalizados nessas consultas por meio do Azure Monitor.

2.  Você quer monitorar os data factories. Você pode encaminhar os dados de diversos data factories para um único workspace do Azure Monitor.

Para uma introdução de sete minutos e uma demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Definir as Configurações de Diagnóstico e de Workspace

Habilite as configurações de diagnóstico para seu data factory.

1. No portal, navegue até Azure Monitor e clique em **configurações de diagnóstico** no menu **configurações** .

2. Selecione o data factory para o qual você gostaria de definir uma configuração de diagnóstico.
    
3. Se não houver configurações no data factory que você selecionou, será solicitado que você crie uma configuração. Clique em “Ativar diagnóstico”.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se houver configurações existentes no data factory, você verá uma lista de configurações já configuradas neste data factory. Clique em "Adicionar configuração de diagnóstico".

   ![Adicionar configuração de diagnóstico - configurações existentes](media/data-factory-monitor-oms/add-diagnostic-setting.png)

4. Dê um nome à sua configuração e marque a caixa **Enviar para o Log Analytics**, em seguida, selecione um espaço de trabalho do Log Analytics.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

5. Clique em **Salvar**.

Após alguns instantes, a nova configuração aparecerá na lista de configurações dessa data factory e os logs de diagnóstico serão transmitidos para esse espaço de trabalho assim que novos dados de evento forem gerados. Pode haver até 15 minutos entre o momento em que um evento é emitido e quando ele aparece no Log Analytics.

> [!NOTE]
> Devido a um limite explícito de uma determinada tabela de log do Azure não ter mais de 500 colunas, **é altamente recomendável usar o modo específico do recurso**. Para obter mais informações, consulte [log Analytics limitações conhecidas](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instale o Azure Data Factory Analytics pelo Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Clique em **Criar** e selecione o Workspace e as configurações do Workspace.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorar Métricas do Data Factory

A instalação do **Azure Data Factory Analytics** cria um conjunto padrão de exibições que permite as seguintes métricas:

- Execuções do ADF – 1) Execuções de pipeline por Data Factory

- Execuções do ADF – 2) Execuções de atividade por Data Factory

- Execuções do ADF – 3) Execuções de gatilho por Data Factory

- Erros do ADF – 1) 10 principais erros de pipeline por Data Factory

- Erros do ADF – 2) 10 principais execuções de atividade por Data Factory

- Erros do ADF – 3) 10 principais erros de gatilho por Data Factory

- Estatísticas do ADF – 1) Execuções de atividade por Tipo

- Estatísticas do ADF – 2) Execuções de gatilho por Tipo

- Estatísticas do ADF – 3) Duração máx. de execuções de pipeline

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Você pode visualizar as métricas acima, examinar as consultas por trás dessas métricas, editar as consultas, criar alertas e assim por diante.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Alertas

Entre no portal do Azure e clique em **monitorar** > **alertas** para criar alertas.

![Alertas no menu do portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Criar alertas

1.  Clique em **+ Nova regra de alerta** para criar um novo alerta.

    ![Nova regra de alerta](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definir a **Condição de alerta**.

    > [!NOTE]
    > Certifique-se de selecionar **Todos** em **Filtrar por tipo de recurso**.

    ![Condição do alerta, tela 1 de 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Condição do alerta, tela 2 de 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Condição do alerta, tela 3 de 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Defina os **detalhes do Alerta**.

    ![Detalhes do Alerta](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Defina o **Grupo de Ação**.

    ![Grupo de ação, tela de 1 de 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Grupo de ação, tela de 2 de 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Grupo de ação, tela de 3 de 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Grupo de ação, tela de 4 de 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Próximas etapas

Consulte o artigo [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md) para saber mais sobre o monitoramento e o gerenciamento de pipelines com código.

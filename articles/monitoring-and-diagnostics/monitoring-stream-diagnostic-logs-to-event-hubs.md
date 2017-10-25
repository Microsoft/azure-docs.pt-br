---
title: "Transmitir Logs de Diagnóstico do Azure para um Namespace de Hubs de Eventos | Microsoft Docs"
description: "Saiba como transmitir logs de diagnóstico do Azure para um namespace de Hubs de Eventos."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem
ms.openlocfilehash: 01ba8ddfcf90e1368ac147296fd180f99420d96f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hubs-namespace"></a>Transmitir Logs de Diagnóstico do Azure para um Namespace de Hubs de Eventos
Os **[logs de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)** podem ser transmitidos em tempo real a qualquer aplicativo usando a opção interna "Exportar para Hubs de Eventos" no Portal, ou habilitando a ID da Regra de Barramento de Serviço em uma configuração de diagnóstico por meio de cmdlets do Azure PowerShell ou da CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>O que você pode fazer com os logs de diagnóstico e os Hubs de Eventos
Veja algumas maneiras de usar o recurso de streaming para os Logs de Diagnóstico:

* **Transmitir logs para sistemas de registro em log e telemetria de terceiros** – ao longo do tempo, a transmissão de Hubs de Eventos se tornará o mecanismo para direcionar seus logs de diagnóstico para SIEMs e soluções de análise de log de terceiros.
* **Exibir a integridade do serviço transmitindo dados de "afunilamento" para o PowerBI** – com os Hubs de Eventos, Stream Analytics e o PowerBI, é fácil transformar seus dados de diagnóstico em informações quase em tempo real nos serviços do Azure. [Este artigo de documentação apresenta uma excelente visão geral de como configurar Hubs de Eventos, processar dados com o Stream Analytics e usar o PowerBI como saída](../stream-analytics/stream-analytics-power-bi-dashboard.md). Aqui estão algumas dicas para configurá-lo com os logs de diagnóstico:
  
  * Um hub de eventos de uma categoria de logs de diagnóstico é criado automaticamente quando você marca a opção no portal ou habilita-a por meio do PowerShell, de modo que você possa selecionar o hub de evento no namespace com o nome que começa com **insights-**.
  * O código SQL a seguir é um exemplo de consulta do Stream Analytics que você pode usar para analisar todos os dados de log em uma tabela do PowerBI:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the PowerBI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Compilar uma plataforma de registro em log e telemetria personalizada** – Se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de logs de diagnóstico. [Confira o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Habilitar o streaming de logs de diagnóstico
Você pode habilitar programaticamente o streaming de logs de diagnóstico por meio do portal ou usando a [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). De qualquer forma, você cria uma configuração de diagnóstico no qual especifica um namespace de Hubs de Eventos e as categorias de log e as métricas que deseja enviar para o namespace. Um hub de eventos é criado no namespace para cada categoria de log que você habilitar. Uma **categoria de log** de diagnóstico é um tipo de log que um recurso pode coletar.

> [!WARNING]
> A habilitação e streaming de logs de diagnóstico dos recursos de computação (por exemplo, VMs ou Service Fabric) [exige um conjunto diferente de etapas](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

O namespace do Barramento de Serviço ou dos Hubs de Eventos não precisa estar na mesma assinatura que o recurso que emite os logs, desde que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Transmitir logs de diagnóstico usando o portal
1. No portal, navegue até o Azure Monitor e clique em **Configurações de Diagnóstico**

    ![Seção de monitoramento do Azure Monitor](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Se desejar filtrar a lista por tipo de recurso ou grupo de recursos, clique no recurso para o qual você deseja definir uma configuração de diagnóstica.

3. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em “Ativar diagnóstico”.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já definidas nesse recurso. Clique em "Adicionar configuração de diagnóstico".

   ![Adicionar configuração de diagnóstico - configurações existentes](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Dê um nome à sua configuração e marque a caixa **Fluxo para um hub de eventos** e então selecione um namespace de Hubs de Eventos.
   
   ![Adicionar configuração de diagnóstico - configurações existentes](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)
    
   O namespace selecionado será o local em que o hub de evento é criado (se este for seu primeiro streaming de logs de diagnóstico) ou transmitido (se já houver recursos realizando o streaming dessa categoria log para esse namespace) e a política define as permissões do mecanismo de streaming. Atualmente, o streaming para um hub de eventos exige as permissões para Gerenciar, Enviar e Escutar. Você pode criar ou modificar políticas de acesso compartilhado de namespace de Hubs de Eventos no portal na guia Configurar para seu namespace. Para atualizar uma dessas configurações de diagnóstico, o cliente deve ter a permissão ListKey na regra de autorização de Hubs de Evento.

4. Clique em **Salvar**.

Após alguns instantes, a nova configuração aparece na lista de configurações para esse recurso e os logs de diagnóstico são transmitidos para a conta de armazenamento assim que os novos dados de evento são gerados.

### <a name="via-powershell-cmdlets"></a>Via Cmdlets do PowerShell
Para habilitar o streaming por meio de [Cmdlets do Azure PowerShell](insights-powershell-samples.md), use o cmdlet `Set-AzureRmDiagnosticSetting` com estes parâmetros:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -ServiceBusRuleId [your Service Bus rule ID] -Enabled $true
```

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{Service Bus resource ID}/authorizationrules/{key name}` , `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{Service Bus namespace}/authorizationrules/RootManageSharedAccessKey`.

### <a name="via-azure-cli"></a>Via CLI do Azure
Para habilitar o streaming por meio da [CLI do Azure](insights-cli-samples.md), use o comando `insights diagnostic set` da seguinte forma:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --serviceBusRuleId <serviceBusRuleID> --enabled true
```

Use o mesmo formato para ID de Regra do Barramento de Serviço, conforme explicado para o Cmdlet do PowerShell.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Como eu consumo os dados de log dos Hubs de Eventos?
Aqui está um exemplo de dados de saída dos Hubs de Eventos:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nome do elemento | Descrição |
| --- | --- |
| records |Uma matriz de todos os eventos de log nessa carga. |
| tempo real |A hora na qual o evento ocorreu. |
| categoria |Categoria do log desse evento. |
| resourceId |ID de recurso do recurso que gerou esse evento. |
| operationName |Nome da operação. |
| level |Opcional. Indica o nível do evento de log. |
| propriedades |Propriedades do evento. |

É possível exibir uma lista de todos os provedores de recursos que dão suporte a streaming para o Hub de Eventos [aqui](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Transmitir dados de recursos de Computação
Também é possível transmitir logs de diagnóstico de recursos de Computação usando o agente do Diagnóstico do Azure. [Consulte este artigo](../event-hubs/event-hubs-streaming-azure-diags-data.md) para saber como configurar isso.

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os Logs de Diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)
* [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


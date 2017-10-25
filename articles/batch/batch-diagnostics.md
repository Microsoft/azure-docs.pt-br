---
title: "Habilitar o log de diagnóstico para Eventos em lote - Azure | Microsoft Docs"
description: "Registre e analisar eventos de log de diagnóstico para recursos de conta do Lote do Azure, como pools e tarefas."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7bc6fd9921ab0f2374ace33ea5c1ab93a78f860
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Eventos de log para avaliação de diagnóstico e monitoramento de soluções do Lote

Assim como acontece com muitos serviços do Azure, o serviço de Lote emite eventos de log para certos recursos durante a vida útil do recurso. Você pode habilitar logs de diagnóstico do Lote do Azure para registrar eventos de recursos, como pools e tarefas, e usar os logs para a avaliação e monitoramento de diagnóstico. Eventos como criação de pool, exclusão de pool, início da tarefa, conclusão de tarefa e outros são incluídos nos logs de diagnóstico do Lote.

> [!NOTE]
> Este artigo descreve os eventos de registro em log para os próprios recursos de conta do Lote, e não para os dados de saída de trabalhos e tarefas. Para obter detalhes sobre como armazenar os dados de saída de seus trabalhos e tarefas, confira [Persistir a saída de trabalhos e tarefas do Lote do Azure](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* [Conta do Lote do Azure](batch-account-create-portal.md)
* [Conta do Armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Para persistir os logs de diagnóstico do Lote, você deve criar uma conta de Armazenamento do Azure na qual o Azure armazenará os logs. Especifique a conta de Armazenamento quando você [Habilitar o registro em log de diagnóstico](#enable-diagnostic-logging) para sua conta do Lote. A conta de Armazenamento especificada quando você habilita a coleta de log não é a mesma que uma conta de armazenamento vinculada citada nos artigos [pacotes de aplicativos](batch-application-packages.md) e [persistência de saída da tarefa](batch-task-output.md).
  
  > [!WARNING]
  > Você é **cobrado** pelos dados armazenados em sua conta de Armazenamento do Azure. Isso inclui os logs de diagnóstico discutidos neste artigo. Lembre-se disso ao projetar sua [política de retenção de log](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Habilitar registro em log de diagnóstico
O registro em log de diagnóstico não está habilitado por padrão para sua conta do Lote. Você deve habilitar explicitamente o registro em log de diagnóstico para cada conta do Lote que você deseja monitorar:

[Como habilitar a coleção de Logs de Diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

Recomendamos que você leia todo o artigo [Visão geral dos Logs de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para entender não apenas como habilitar o registro em log, mas também as categorias de log com suporte dos vários serviços do Azure. Por exemplo, atualmente o Lote do Azure oferece suporte a uma categoria de log: **Logs de Serviço**.

## <a name="service-logs"></a>Logs de serviço
Os Logs de serviço do Lote do Azure contêm os eventos emitidos pelo serviço de Lote do Azure durante o tempo de vida de um recurso do Lote como uma tarefa ou um pool. Cada evento emitido pelo Lote é armazenado na conta de Armazenamento especificada no formato JSON. Por exemplo, este é o corpo de um exemplo de **evento de criação de pool**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Cada corpo de evento reside em um arquivo .json na conta especificada do Armazenamento do Azure. Se você quiser acessar os logs diretamente, talvez você queira examinar o [esquema de Logs de Diagnóstico na conta de armazenamento](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Eventos do Log de Serviço
O serviço do Lote emite atualmente os seguintes eventos do Log de Serviço. Essa lista pode não ser completa, pois outros eventos podem ter sido adicionados desde a última atualização deste artigo.

| **Eventos do Log de Serviço** |
| --- |
| [Criação de pool][pool_create] |
| [Início de exclusão de pool][pool_delete_start] |
| [Conclusão de exclusão de pool][pool_delete_complete] |
| [Início de redimensionamento de pool][pool_resize_start] |
| [Conclusão de redimensionamento de pool][pool_resize_complete] |
| [Início de tarefa][task_start] |
| [Conclusão de tarefa][task_complete] |
| [Falha de tarefa][task_fail] |

## <a name="next-steps"></a>Próximas etapas
Além de armazenar os eventos do log de diagnóstico em uma conta de Armazenamento do Azure, você também pode transmitir eventos do Log de Serviço do Lote para um [Hub de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md), e enviá-los para o [Azure Log Analytics](../log-analytics/log-analytics-overview.md).

* [Transmitir Logs de Diagnóstico do Azure para os Hubs de Eventos](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Transmita os eventos de diagnóstico do Lote para o serviço de entrada de dados altamente dimensionável, Hubs de Eventos. Os Hubs de Eventos podem incluir milhões de eventos por segundo, os quais você pode transformar e armazenar usando qualquer provedor de análise em tempo real.
* [Analisar logs de diagnóstico do Azure usando o Log Analytics](../log-analytics/log-analytics-azure-storage.md)
  
  Envie seus logs de diagnóstico para o Log Analytics, onde você pode analisá-los no portal do OMS (Operations Management Suite) ou exportá-los para análise no Power BI ou Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx

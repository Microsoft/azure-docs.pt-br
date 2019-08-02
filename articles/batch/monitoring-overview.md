---
title: Monitoramento do Lote do Azure | Microsoft Docs
description: Saiba mais sobre os serviços de monitoramento, métrica, logs de diagnóstico do Azure e outros recursos de monitoramento para Lote do Azure.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: 77fcfed125104588e27e6a0e80cdb4a92a744083
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322445"
---
# <a name="monitor-batch-solutions"></a>Monitorar soluções do Lote

O Azure e o serviço do Lote fornecem uma variedade de serviços, ferramentas e APIs para monitorar suas soluções em Lote. Este artigo de visão geral ajuda-o a escolher uma abordagem de monitoramento que atenda às suas necessidades.

Para obter uma visão geral dos componentes e serviços do Azure disponíveis para monitorar recursos do Azure, consulte [Monitorar aplicativos e recursos do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Monitoramento no nível da assinatura

No nível da assinatura, que inclui as contas do Lote, o [Log de atividades](../azure-monitor/platform/activity-logs-overview.md) coleta dados do evento operacional em [várias categorias](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log).

Para as contas do Lote e especificamente, o log de atividades coleta eventos relacionados à criação e exclusão de contas e ao gerenciamento de chaves.

Uma maneira de recuperar eventos do log de atividades é usar o Portal do Azure. Clique em **Todos os serviços** > **Log de Atividades**. Ou consulte os eventos usando as CLI do Azure, os cmdlets do PowerShell ou a API REST do Azure Monitor. Você também pode exportar o log de atividades ou configurar os [alertas do log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Monitoramento no nível de conta do Lote

Monitore cada conta do Lote usando os recursos do [Azure Monitor](../azure-monitor/overview.md). O Azure Monitor coleta [métricas](../azure-monitor/platform/data-platform-metrics.md) e, opcionalmente, [logs de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) para recursos com escopo no nível de uma conta do Lote, como pools, trabalhos e tarefas. Colete e consuma esses dados manualmente ou programaticamente para monitorar atividades na conta do Lote e para diagnosticar problemas. Para obter detalhes, consulte [Logs, alertas e métricas do Lote para avaliação e monitoramento de diagnóstico](batch-diagnostics.md).
 
> [!NOTE]
> As métricas estão disponíveis por padrão na conta do Lote sem configuração adicional e têm um histórico de 30 dias. Você deve habilitar o log de diagnóstico para uma conta do Lote e pode incorrer em custos adicionais para armazenar ou processar dados de log de diagnóstico. 

## <a name="batch-resource-monitoring"></a>Monitoramento de recursos do Lote

Nos aplicativo do Lote, use as APIs de Lote para monitorar ou consultar o status dos recursos, incluindo trabalhos, tarefas, nós e pools. Por exemplo:

* [Contar tarefas e nós de computação por estado](batch-get-resource-counts.md)
* [Criar consultas para listar os recursos do Lote de forma eficiente](batch-efficient-list-queries.md)
* [Criar dependências de tarefas](batch-task-dependencies.md)
* Use uma [tarefa do gerenciador de trabalhos](/rest/api/batchservice/job/add#jobmanagertask)
* Monitore o [estado da tarefa](/rest/api/batchservice/task/list#taskstate)
* Monitore o [estado do nó](/rest/api/batchservice/computenode/list#computenodestate)
* Monitore o [estado do pool](/rest/api/batchservice/pool/get#poolstate)
* Monitore o [uso do pool na conta](/rest/api/batchservice/pool/listusagemetrics)
* [Contagem de nós do pool por estado](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Contadores de desempenho de VM e monitoramento de aplicativos

* O [Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço do Azure que você pode usar para monitorar programaticamente a disponibilidade, o desempenho e o uso dos trabalhos e tarefas do Lote. Obtenha facilmente contadores de desempenho de nós de computação (VMs) e informações personalizadas para tarefas fora das VMs. 

  Para um exemplo, consulte [Monitorar e depurar um aplicativo .NET do Lote com o Application Insights](monitor-application-insights.md) e o [exemplo de código](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) que acompanha.

  > [!NOTE]
  > Custos adicionais poderão incorrer para usar o Application Insights. Consulte [opções de preços](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [O Batch Explorer](https://github.com/Azure/BatchExplorer) é uma ferramenta cliente autônoma, rica e exclusiva para ajudar a criar, depurar e monitorar aplicativos em lote do Azure. Baixe um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows. Opcionalmente, configure sua solução em lote para [exibir dados do Application Insights](https://github.com/Azure/batch-insights), como os contadores de desempenho da VM no Batch Explorer.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
* Saiba mais sobre [log de diagnósticos](batch-diagnostics.md) com Lote.
---
title: Métricas, alertas e logs de diagnóstico - Lote do Azure | Microsoft Docs
description: Registre e analisar eventos de log de diagnóstico para recursos de conta do Lote do Azure, como pools e tarefas.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 63d0196609e432b081e91a49b5b1410431223632
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323618"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Logs, alertas e métricas do Lote para avaliação e monitoramento de diagnóstico

 
Este artigo explica como monitorar uma conta do Lote usando os recursos do [Azure Monitor](../azure-monitor/overview.md). O Azure Monitor coleta [métricas](../azure-monitor/platform/data-platform-metrics.md) e [logs de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) para recursos na conta do Lote. Colete e consuma esses dados de várias maneiras para monitorar a conta do Lote e diagnosticar problemas. Também é possível configurar [alertas de métrica](../azure-monitor/platform/alerts-overview.md) para receber notificações quando uma métrica alcançar um valor especificado. 

## <a name="batch-metrics"></a>Métricas do Lote

Métricas são dados telemétricos do Azure (também chamados de contadores de desempenho) emitidos pelos recursos do Azure que são consumidos pelo serviço Azure Monitor. As métricas de exemplo em uma conta do Lote incluem: Eventos de Criação de Pool, Contagem de Nós de Baixa Prioridade e Eventos de Conclusão de Tarefa. 

Consulte [lista de métricas do Lote com suporte](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Métricas são:

* Habilitadas por padrão em cada conta do Lote sem configuração adicional
* Geradas a cada 1 minuto
* Não são persistidas automaticamente, mas têm um histórico de 30 dias. É possível persistir métricas de atividades como parte do log de diagnósticos.

### <a name="view-metrics"></a>Métricas de exibição

Visualize as métricas para a conta do Lote no Portal do Azure. A **Visão Geral** da conta, por padrão, mostra as principais métricas de tarefa, núcleo e nó. 

Para exibir todas as métricas da conta do lote: 

1. Clique em **Todos os serviços** > **Contas do Lote** e, em seguida, clique no nome da conta do Lote.
2. Em **Monitoramento**, clique em **Métrica**.
3. Selecione uma ou mais métricas. Se preferir, selecione as métricas de recursos adicionais usando as listas suspensas **Assinaturas**, **Grupo de recursos**, **Tipo de recurso** e **Recurso**.

    ![Métricas do Lote](media/batch-diagnostics/metrics-portal.png)

Para recuperar métricas programaticamente, use as APIs do Azure Monitor. Por exemplo, consulte [Recuperar métricas do Azure Monitor com .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Confiabilidade de métrica em lote

As métricas destinam-se a serem usadas para a análise de tendências e de dados. A entrega da métrica não é garantida e está sujeita a entrega fora de ordem, perda de dados e/ou duplicação. Não é recomendável usar eventos individuais para funções de gatilho ou de alerta. Confira a seção [Alertas de métrica em lote](#batch-metric-alerts) para obter mais detalhes sobre como definir limites para alertas.

As métricas emitidas nos últimos 3 minutos ainda podem estar sendo agregadas. Durante esse período, os valores de métricas podem ser relatados de forma insuficiente.

## <a name="batch-metric-alerts"></a>Alertas de métrica do Lote

Opcionalmente, configure os *alertas de métrica* quase em tempo real que são disparados quando o valor de uma métrica especificada ultrapassa um limite atribuído. O alerta gera uma [notificação](../monitoring-and-diagnostics/insights-alerts-portal.md) escolhida quando o alerta for "Ativado" (quando o limite é ultrapassado e a condição de alerta é atendida), bem como quando for "Resolvido" (quando o limite é ultrapassado novamente e a condição não é mais atendida). Não é recomendável disparar alertas com base em pontos de dados individuais, pois as métricas estão sujeitas a entrega fora de ordem, perda de dados e/ou duplicação. Os alertas deverão fazer uso de limites para levar em conta essas inconsistências.

Por exemplo, você pode configurar um alerta de métrica quando a contagem de núcleos de baixa prioridade cair para um determinado nível, de modo que seja possível ajustar a composição dos pools. É recomendável definir um período de 10 minutos ou mais no qual os alertas dispararão se a contagem média de núcleos de baixa prioridade estiver abaixo do valor limite para o período inteiro. Não é recomendável emitir um alerta em um período de 1 a 5 minutos, pois as métricas ainda podem estar sendo agregadas.

Para configurar um alerta de métrica no portal:

1. Clique em **Todos os serviços** > **Contas do Lote** e, depois, clique no nome de sua conta do Lote.
2. Em **Monitoramento**, clique em **Regras de alerta** > **Adicionar métrica de alerta**.
3. Selecione uma métrica, uma condição de alerta (por exemplo, quando uma métrica exceder um valor específico durante um período) e uma ou mais notificações.

Também é possível configurar um alerta quase em tempo real usando a [API REST](https://docs.microsoft.com/rest/api/monitor/). Para obter mais informações, confira [Visão geral de alertas](../azure-monitor/platform/alerts-overview.md)

## <a name="batch-diagnostics"></a>Diagnóstico do Lote

Os logs de diagnóstico contêm informações emitidas pelos recursos do Azure que descrevem a operação de cada recurso. Para o Lote, é possível coletar os logs a seguir:

* Eventos de **Logs de Serviço** emitidos pelo serviço do Lote do Azure durante o tempo de vida de um recurso individual do Lote, como um pool ou uma tarefa. 

* Logs de **Métrica** no nível da conta. 

As configurações para habilitar a coleta de logs de diagnóstico não são habilitadas por padrão. Habilite explicitamente as configurações de diagnóstico para cada conta do Lote que você quer monitorar.

### <a name="log-destinations"></a>Destinos do log

Um cenário comum é selecionar uma conta de Armazenamento do Microsoft Azure como o destino do log. Para armazenar logs no Armazenamento do Microsoft Azure, crie a conta antes de habilitar a coleção dos logs. Se você associou uma conta de armazenamento à conta do Lote, poderá escolher essa conta como o destino do log. 

Outros destinos opcionais para logs de diagnóstico:

* Transmita eventos de log de diagnóstico do Lote para um [Hub de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Os Hubs de Eventos podem incluir milhões de eventos por segundo, os quais você pode transformar e armazenar usando qualquer provedor de análise em tempo real. 

* Envie logs de diagnóstico para [Azure monitor logs](../log-analytics/log-analytics-overview.md), onde você pode analisá-los ou exportá-los para análise no Power bi ou no Excel.

> [!NOTE]
> É possível que incorra custos adicionais para armazenar ou processar dados de log de diagnóstico com os serviços do Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Habilitar coleção de logs de diagnóstico do Lote

1. Clique em **Todos os serviços** > **Contas do Lote** e, em seguida, clique no nome da conta do Lote.
2. Em **Monitoramento**, clique em **Logs de diagnóstico** > **Ativar diagnóstico**.
3. Em **configurações de diagnóstico**, insira um nome para a configuração e escolha um destino de log (conta de armazenamento existente, Hub de eventos ou logs de Azure monitor). Selecione um ou ambos **ServiceLog** e **AllMetrics**.

    Ao selecionar uma conta de armazenamento, opcionalmente, defina uma política de retenção. Se você não especificar um número de dias para retenção, os dados serão retidos durante a vida útil da conta de armazenamento.

4. Clique em **Salvar**.

    ![Diagnóstico do Lote](media/batch-diagnostics/diagnostics-portal.png)

Outras opções para habilitar a coleção de logs incluem: use o Azure Monitor no portal para definir configurações de diagnóstico, use um [modelo do Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md) ou use o Azure PowerShell ou a CLI do Azure. consulte [Coletar e consumir dados de log dos recursos do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Acessar logs de diagnóstico no armazenamento

Se você arquivar logs de diagnóstico do Lote em uma conta de armazenamento, um contêiner de armazenamento será criado na conta de armazenamento assim que ocorrer um evento relacionado. Os blobs são criados de acordo com o padrão de nomenclatura a seguir:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Exemplo:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Cada arquivo blob PT1H.json contém eventos formatados em JSON que ocorreram na hora especificada na URL do blob (por exemplo, h = 12). Durante a hora presente, os eventos são acrescentados ao arquivo PT1H.json conforme eles ocorrem. O valor de minuto (m=00) é sempre 00, como eventos de logs de diagnóstico são divididos em blobs individuais por hora. (Todas as horas estão em UTC.)


Para obter mais informações sobre o esquema de logs de diagnóstico na conta de armazenamento, consulte [Arquivar logs de diagnóstico do Azure](../azure-monitor/platform/archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Para acessar os logs na conta de armazenamento programaticamente, use as APIs de Armazenamento. 

### <a name="service-log-events"></a>Eventos do Log de Serviço
Logs de Serviço do Lote do Azure, se coletados, contêm eventos emitidos pelo serviço do Lote do Azure durante o tempo de vida de um recurso individual do Lote, como um pool ou uma tarefa. Cada evento emitido pelo Lote é registrado em formato JSON. Por exemplo, este é o corpo de um exemplo de **evento de criação de pool**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
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

O serviço do Lote emite atualmente os seguintes eventos do Log de Serviço. Essa lista pode não ser completa, pois outros eventos podem ter sido adicionados desde a última atualização deste artigo.

| **Eventos do Log de Serviço** |
| --- |
| [Criação de pool](batch-pool-create-event.md) |
| [Início de exclusão de pool](batch-pool-delete-start-event.md) |
| [Conclusão da exclusão de pool](batch-pool-delete-complete-event.md) |
| [Início de redimensionamento de pool](batch-pool-resize-start-event.md) |
| [Conclusão de redimensionamento de pool](batch-pool-resize-complete-event.md) |
| [Início da tarefa](batch-task-start-event.md) |
| [Conclusão da tarefa](batch-task-complete-event.md) |
| [Falha da tarefa](batch-task-fail-event.md) |



## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
* Saiba mais sobre [Monitorar soluções do Lote](monitoring-overview.md).

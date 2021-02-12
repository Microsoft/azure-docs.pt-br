---
title: Métricas, alertas e logs de diagnóstico
description: Registre e analisar eventos de log de diagnóstico para recursos de conta do Lote do Azure, como pools e tarefas.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18
ms.openlocfilehash: fe2697c73f2a5f3f0b33cfb598f11f39420ed723
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994104"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Logs, alertas e métricas do Lote para avaliação e monitoramento de diagnóstico

Este artigo explica como monitorar uma conta do Lote usando os recursos do [Azure Monitor](../azure-monitor/overview.md). O Azure Monitor coleta [métricas](../azure-monitor/platform/data-platform-metrics.md) e [logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md) para recursos na conta do Lote. Colete e consuma esses dados de várias maneiras para monitorar a conta do Lote e diagnosticar problemas. Também é possível configurar [alertas de métrica](../azure-monitor/platform/alerts-overview.md) para receber notificações quando uma métrica alcançar um valor especificado.

## <a name="batch-metrics"></a>Métricas do Lote

Métricas são dados de telemetria do Azure (também chamados de contadores de desempenho) que são emitidos por seus recursos do Azure e consumidos pelo serviço de Azure Monitor. Exemplos de métricas em uma conta do lote são eventos de criação de pool, Low-Priority contagem de nós e eventos de conclusão de tarefa.

Consulte [lista de métricas do Lote com suporte](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Métricas são:

- Habilitadas por padrão em cada conta do Lote sem configuração adicional
- Geradas a cada 1 minuto
- Não são persistidas automaticamente, mas têm um histórico de 30 dias. É possível persistir métricas de atividades como parte do log de diagnósticos.

## <a name="view-batch-metrics"></a>Exibir métricas do lote

Na portal do Azure, a página **visão geral** da conta mostrará as principais métricas de nó, núcleo e tarefa por padrão.

Para exibir todas as métricas de conta do lote no portal do Azure:

1. Na portal do Azure, selecione **todos os serviços**  >  **contas do lote** e, em seguida, selecione o nome da sua conta do lote.
2. Em **Monitoramento**, selecione **Métricas**.
3. Selecione **Adicionar métrica** e, em seguida, escolha uma métrica na lista suspensa.
4. Selecione uma opção de **agregação** para a métrica. Para métricas baseadas em contagem (como "contagem de núcleos dedicados" ou "contagem de nós de baixa prioridade"), use a agregação **média** . Para métricas baseadas em evento (como "eventos de redimensionamento de pool concluídos"), use a agregação " **Count**".

   > [!WARNING]
   > Não use a agregação "Sum", que soma os valores de todos os pontos de dados recebidos no período do gráfico.

5. Para adicionar métricas adicionais, repita as etapas 3 e 4.

Você também pode recuperar métricas programaticamente com as APIs de Azure Monitor. Para obter um exemplo, consulte [recuperar Azure monitor métricas com o .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

### <a name="batch-metric-reliability"></a>Confiabilidade de métrica em lote

As métricas podem ajudar a identificar tendências e podem ser usadas para análise de dados. É importante observar que a entrega de métrica não é garantida e pode estar sujeita a entrega fora de ordem, perda de dados e/ou duplicação. Por isso, o uso de eventos únicos para alertar ou disparar funções não é recomendado. Consulte a próxima seção para obter mais detalhes sobre como definir limites para alertas.

As métricas emitidas nos últimos 3 minutos ainda podem ser agregadas, de modo que os valores de métrica podem ser subportados durante esse período.

## <a name="batch-metric-alerts"></a>Alertas de métrica do Lote

Você pode configurar *alertas de métrica* quase em tempo real que são disparados quando o valor de uma métrica especificada cruza um limite que você atribui. O alerta gera uma notificação quando o alerta é "Ativado" (quando o limite for ultrapassado, e a condição do alerta for atendida), e também quando for "Resolvido" (quando o limite for ultrapassado novamente e a condição não for mais atendida).

Alertas que disparam em um único ponto de dados não são recomendados, pois as métricas estão sujeitas a entrega fora de ordem, perda de dados e/ou duplicação. Ao criar seus alertas, você pode usar os limites para considerar essas inconsistências.

Por exemplo, você pode configurar um alerta de métrica quando a contagem de núcleos de baixa prioridade cair para um determinado nível, de modo que seja possível ajustar a composição dos pools. Para obter melhores resultados, defina um período de 10 ou mais minutos, em que os alertas são disparados se a contagem de núcleos de baixa prioridade baixo estiver abaixo do valor de limite para o período inteiro. Isso permite mais tempo para que as métricas sejam agregadas para que você obtenha resultados mais precisos.

Para configurar um alerta de métrica no portal do Azure:

1. Selecione **Todos os serviços** > **Contas do Lote** e, em seguida, selecione o nome de sua conta do Lote.
2. Em **monitoramento**, selecione **alertas** e, em seguida, selecione **nova regra de alerta**.
3. Clique em **Selecionar condição** e escolha uma métrica. Confirme os valores do **período do gráfico**, do **tipo de limite**, do **operador** e do **tipo de agregação** e insira um **valor de limite**. Em seguida, selecione **Concluído**.
4. Adicione um grupo de ações ao alerta selecionando um grupo de ações existente ou criar um novo.
5. Na seção **detalhes da regra de alerta** , insira um nome e uma **Descrição** da **regra de alerta** e selecione a **severidade**
6. Selecione **Criar regra de alerta**.

Para obter mais informações sobre como criar alertas de métrica, consulte [entender como os alertas de métrica funcionam no Azure monitor](../azure-monitor/platform/alerts-metric-overview.md) e [criar, exibir e gerenciar alertas de métrica usando Azure monitor](../azure-monitor/platform/alerts-metric.md).

Você também pode configurar um alerta quase em tempo real usando a [API REST](/rest/api/monitor/)do Azure monitor. Para obter mais informações, consulte [visão geral de alertas no Microsoft Azure](../azure-monitor/platform/alerts-overview.md). Para incluir informações específicas de trabalho, tarefa ou pool em seus alertas, consulte as informações em consultas de pesquisa em [responder a eventos com Azure monitor alertas](../azure-monitor/learn/tutorial-response.md).

## <a name="batch-diagnostics"></a>Diagnóstico do Lote

Os logs de diagnóstico contêm informações emitidas pelos recursos do Azure que descrevem a operação de cada recurso. Para o Lote, é possível coletar os logs a seguir:

- Eventos de **Logs de Serviço** emitidos pelo serviço do Lote do Azure durante o tempo de vida de um recurso individual do Lote, como um pool ou uma tarefa.
- Logs de **Métrica** no nível da conta.

As configurações para habilitar a coleta de logs de diagnóstico não são habilitadas por padrão. Habilite explicitamente as configurações de diagnóstico para cada conta do Lote que você quer monitorar.

### <a name="log-destinations"></a>Destinos do log

Um cenário comum é selecionar uma conta de Armazenamento do Microsoft Azure como o destino do log. Para armazenar logs no Armazenamento do Microsoft Azure, crie a conta antes de habilitar a coleção dos logs. Se você associou uma conta de armazenamento à conta do Lote, poderá escolher essa conta como o destino do log.

Como alternativa, você pode:

- Transmita eventos de log de diagnóstico do Lote para um [Hub de Eventos do Azure](../event-hubs/event-hubs-about.md). Os Hubs de Eventos podem incluir milhões de eventos por segundo, os quais você pode transformar e armazenar usando qualquer provedor de análise em tempo real.
- Envie os logs de diagnóstico para o [logs do Azure Monitor](../azure-monitor/log-query/log-query-overview.md), onde você pode analisá-los ou exportá-los para análise no Power BI ou Excel.

> [!NOTE]
> É possível que incorra custos adicionais para armazenar ou processar dados de log de diagnóstico com os serviços do Azure.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Habilitar coleção de logs de diagnóstico do Lote

Para criar uma nova configuração de diagnóstico no portal do Azure, siga as etapas abaixo.

1. Na portal do Azure, selecione **todos os serviços**  >  **contas do lote** e, em seguida, selecione o nome da sua conta do lote.
2. Em **Monitoramento**, selecione **Configurações de diagnóstico**.
3. Em **configurações de diagnóstico**, selecione **Adicionar configuração de diagnóstico**.
4. Insira um nome para a configuração.
5. Selecione um destino: **Enviar para log Analytics**, **arquivar em uma conta de armazenamento** ou **transmitir para um hub de eventos**. Se você selecionar uma conta de armazenamento, poderá, opcionalmente, definir uma política de retenção. Se você não especificar um número de dias para retenção, os dados serão retidos durante a vida útil da conta de armazenamento.
6. Selecione **ServiceLog**, **biometria** ou ambos.
7. Selecione **salvar** para criar a configuração de diagnóstico.

Você também pode [habilitar a coleta por meio de Azure monitor no portal do Azure](../azure-monitor/platform/diagnostic-settings.md) para definir configurações de diagnóstico, usando um [modelo do Resource Manager](../azure-monitor/samples/resource-manager-diagnostic-settings.md)ou com Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [visão geral dos logs da plataforma Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Acessar logs de diagnóstico no armazenamento

Se você arquivar logs de diagnóstico do Lote em uma conta de armazenamento, um contêiner de armazenamento será criado na conta de armazenamento assim que ocorrer um evento relacionado. Os blobs são criados de acordo com o padrão de nomenclatura a seguir:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Cada arquivo `PT1H.json` contém eventos formatados em JSON que ocorreram na hora especificada na URL do blob (por exemplo, `h=12`). Durante a hora presente, os eventos são acrescentados ao arquivo `PT1H.json` conforme eles ocorrem. O valor de minuto (`m=00`) é sempre `00`, como eventos de logs de diagnóstico são divididos em blobs individuais por hora. (Todas as horas estão em UTC.)

Veja abaixo um exemplo de uma entrada `PoolResizeCompleteEvent` em um arquivo de log `PT1H.json`. Ele inclui informações sobre o número atual e de destino de nós dedicados e de baixa prioridade, bem como a hora de início e de término da operação:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Para obter mais informações sobre o esquema de logs de diagnóstico na conta de armazenamento, consulte [arquivar logs de recursos do Azure para a conta de armazenamento](../azure-monitor/platform/resource-logs.md#send-to-azure-storage). Para acessar os logs na conta de armazenamento programaticamente, use as APIs de Armazenamento.

### <a name="service-log-events"></a>Eventos de log de serviço

Os logs de serviço do lote do Azure, se coletados, contêm eventos emitidos pelo serviço de lote do Azure durante o tempo de vida de um recurso de lote individual, como um pool ou uma tarefa. Cada evento emitido pelo Lote é registrado em formato JSON. Por exemplo, este é o corpo de um exemplo de **evento de criação de pool**:

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
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Os eventos de log de serviço emitidos pelo serviço de lote incluem o seguinte:

- [Criação de pool](batch-pool-create-event.md)
- [Início de exclusão de pool](batch-pool-delete-start-event.md)
- [Conclusão da exclusão de pool](batch-pool-delete-complete-event.md)
- [Início de redimensionamento de pool](batch-pool-resize-start-event.md)
- [Conclusão de redimensionamento de pool](batch-pool-resize-complete-event.md)
- [Dimensionamento automático do pool](batch-pool-autoscale-event.md)
- [Início da tarefa](batch-task-start-event.md)
- [Conclusão da tarefa](batch-task-complete-event.md)
- [Falha da tarefa](batch-task-fail-event.md)
- [Falha no agendamento da tarefa](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Ferramentas e APIs do Lote](batch-apis-tools.md) disponíveis para a criação de soluções do Lote.
- Saiba mais sobre [Monitorar soluções do Lote](monitoring-overview.md).
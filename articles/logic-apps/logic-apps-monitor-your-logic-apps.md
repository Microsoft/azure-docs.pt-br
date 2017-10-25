---
title: "Verificar o status, configurar o log e receber alertas – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Monitorar o status e o desempenho de aplicativos lógicos, registrar dados de diagnóstico e configurar alertas"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 4795f5728d4ce6ff21b97bc3fefd6a53e0c6a11b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorar o status, configurar o log de diagnósticos e ativar alertas para os Aplicativo Lógico do Azure

Depois de [criar e executar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md), verifique seu histórico de execuções, histórico de gatilhos, status e desempenho. Para monitoramento de eventos em tempo real e depuração mais avançada, configure o [log de diagnósticos](#azure-diagnostics) do aplicativo lógico. Dessa forma, você poderá [encontrar e exibir eventos](#find-events), como eventos de gatilho, eventos de execução e eventos de ação. Use também esses [dados de diagnóstico com outros serviços](#extend-diagnostic-data), como o Armazenamento do Azure e os Hubs de Eventos do Azure. 

Para obter notificações sobre falhas ou outros possíveis problemas, configure [alertas](#add-azure-alerts). Por exemplo, você pode criar um alerta que detecta “quando mais de cinco execuções falham em uma hora”. Você também pode configurar o monitoramento, o acompanhamento e o log de forma programática usando as [propriedades e configurações de evento do Diagnóstico do Azure](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Exibir execuções e disparar o histórico do aplicativo lógico

1. Para encontrar o aplicativo lógico no [portal do Azure](https://portal.azure.com), no menu principal do Azure, escolha **Mais serviços**. Na caixa de pesquisa, encontre “aplicativos lógicos” e escolha **Aplicativos lógicos**.

   ![Encontrar o aplicativo lógico](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   O portal do Azure mostra todos os aplicativos lógicos associados à sua assinatura do Azure. 

2. Selecione o aplicativo lógico e, em seguida, escolha **Visão geral**.

   O portal do Azure mostra o histórico de execuções e o histórico de gatilhos do aplicativo lógico. Por exemplo:

   ![Histórico de execuções e histórico de gatilhos do aplicativo lógico](media/logic-apps-monitor-your-logic-apps/overview.png)

   * O **histórico de execuções** mostra todas as execuções do aplicativo lógico. 
   * O **histórico de gatilhos** mostra todas as atividades de gatilho do aplicativo lógico.

   Para obter descrições de status, consulte [Solução de problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Se você não encontrar os dados esperados, na barra de ferramentas, escolha **Atualizar**.

3. Para exibir as etapas de uma execução específica, em **Histórico de execuções**, selecione essa execução. 

   A exibição de monitor mostra cada etapa dessa execução. Por exemplo:

   ![Ações de uma execução específica](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Para obter mais detalhes sobre a execução, escolha **Detalhes da Execução**. Essas informações resumem as etapas, o status, as entradas e as saídas da execução. 

   ![Escolher “Detalhes da Execução”](media/logic-apps-monitor-your-logic-apps/run-details.png)

   Por exemplo, você pode obter a **ID de Correlação** da execução, que pode ser útil ao usar a [API REST dos Aplicativos Lógicos](https://docs.microsoft.com/rest/api/logic).

5. Para obter detalhes sobre uma etapa específica, escolha essa etapa. Agora você pode examinar detalhes como entradas, saídas e todos os erros que ocorreram nessa etapa. Por exemplo:

   ![Detalhes da etapa](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Todos os eventos e detalhes de tempo de execução são criptografados no serviço Aplicativos Lógicos. Eles são descriptografados apenas quando um usuário solicita a exibição desses dados. Controle também o acesso a esses eventos com o [RBAC (Controle de Acesso Baseado em Função) do Azure](../active-directory/role-based-access-control-what-is.md).

6. Para obter detalhes sobre um evento de gatilho específico, volte para o painel **Visão geral**. Em **Histórico de gatilhos**, selecione o evento de gatilho. Agora você pode examinar detalhes como entradas e saídas, por exemplo:

   ![Detalhes de saída do evento de gatilho](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Ativar o log de diagnósticos do aplicativo lógico

Para uma depuração mais avançada com eventos e detalhes de tempo de execução, configure o log de diagnósticos com o [Azure Log Analytics](../log-analytics/log-analytics-overview.md). O Log Analytics é um serviço do [OMS (Operations Management Suite)](../operations-management-suite/operations-management-suite-overview.md) que monitora os ambientes locais e de nuvem para ajudá-lo a manter a disponibilidade e o desempenho deles. 

Antes de começar, você precisa ter um espaço de trabalho do OMS. Saiba [como criar um espaço de trabalho do OMS](../log-analytics/log-analytics-get-started.md).

1. No [portal do Azure](https://portal.azure.com), encontre e selecione o aplicativo lógico. 

2. No menu da folha do aplicativo lógico, em **Monitoramento**, escolha **Diagnóstico** > **Configurações de Diagnóstico**.

   ![Acesse Monitoramento, Diagnóstico, Configurações de Diagnóstico](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. Em **Configurações de diagnóstico**, escolha **Ativado**.

   ![Ativar logs de diagnóstico](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Agora, selecione o espaço de trabalho do OMS e a categoria de evento para o log, conforme mostrado:

   1. Selecione **Enviar para o Log Analytics**. 
   2. Em **Log Analytics**, escolha **Configurar**. 
   3. Em **Espaços de Trabalho do OMS**, selecione o espaço de trabalho do OMS a ser usado para o log.
   4. Em **Log**, selecione a categoria **WorkflowRuntime**.
   5. Escolha o intervalo de métrica.
   6. Quando terminar, escolha **Salvar**.

   ![Selecionar o espaço de trabalho do OMS e os dados para log](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

Agora, você pode encontrar eventos e outros dados para eventos de gatilho, eventos de execução e eventos de ação.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Encontrar eventos e dados do aplicativo lógico

Para localizar e exibir eventos no aplicativo lógico, como eventos de gatilho, eventos de execução e eventos de ação, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com), escolha **Mais serviços**. Pesquise “log analytics” e, em seguida, escolha **Log Analytics**, conforme mostrado aqui:

   ![Escolher “Log Analytics”](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. Em **Log Analytics**, encontre e selecione o espaço de trabalho do OMS. 

   ![Selecionar o espaço de trabalho do OMS](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. Em **Gerenciamento**, escolha **Portal do OMS**.

   ![Escolher “Portal do OMS”](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. Na home page do OMS, escolha **Pesquisa de Logs**.

   ![Na home page do OMS, escolha “Pesquisa de Logs”](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   -ou-

   ![No menu do OMS, escolha “Pesquisa de Logs”](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. Na caixa de pesquisa, especifique um campo que você deseja encontrar e, em seguida, pressione **Enter**. Quando você começa a digitar, o OMS mostra as possíveis correspondências e operações que podem ser usadas. 

   Por exemplo, para encontrar os 10 primeiros eventos que ocorreram, insira e selecione esta consulta de pesquisa: **Category=WorkflowRuntime |top 10**

   ![Inserir a cadeia de pesquisa](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Saiba mais sobre [como encontrar dados no Log Analytics](../log-analytics/log-analytics-log-searches.md).

6. Na página de resultados, na barra à esquerda, escolha o período de tempo que você deseja exibir.
Para refinar a consulta adicionando um filtro, escolha **+Adicionar**.

   ![Escolher período de tempo para os resultados da consulta](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. Em **Adicionar Filtros**, insira o nome do filtro para encontrar o filtro desejado. Selecione o filtro e escolha **+Adicionar**.

   Este exemplo usa a palavra “status” para encontrar eventos com falha em **AzureDiagnostics**.
   Aqui, o filtro de **status_s** já está selecionado.

   ![Selecionar filtro](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. Na barra à esquerda, selecione o valor de filtro que você deseja usar e escolha **Aplicar**.

   ![Selecionar valor de filtro e escolher “Aplicar”](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. Agora, retorne à consulta que está sendo criada. A consulta é atualizada com o filtro e o valor selecionados. Os resultados anteriores agora são filtrados também.

   ![Retornar à consulta com os resultados filtrados](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. Para salvar a consulta para uso futuro, escolha **Salvar**. Saiba [como salvar a consulta](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Estender como e onde usar os dados de diagnóstico com outros serviços

Junto com o Azure Log Analytics, você pode estender a maneira de usar os dados de diagnóstico do aplicativo lógico com outros serviços do Azure, por exemplo: 

* [Arquivar logs do Diagnóstico do Azure no Armazenamento do Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Transmitir logs do Diagnóstico do Azure para os Hubs de Eventos do Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Depois, obtenha o monitoramento em tempo real usando a telemetria e a análise de outros serviços, como o [Stream Analytics do Azure](../stream-analytics/stream-analytics-introduction.md) e o [Power BI](../log-analytics/log-analytics-powerbi.md). Por exemplo:

* [Transmitir dados dos Hubs de Eventos para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analisar dados de streaming com o Stream Analytics e criar um painel de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

De acordo com as opções que você deseja configurar, primeiro [crie uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) ou [crie um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, selecione as opções do local para o qual deseja enviar os dados de diagnóstico:

![Enviar dados para uma conta de armazenamento do Azure ou um hub de eventos](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> Os períodos de retenção se aplicam somente quando você opta por usar uma conta de armazenamento.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Configurar alertas para o aplicativo lógico

Para monitorar métricas específicas ou limites excedidos do aplicativo lógico, configure [alertas no Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). Saiba mais sobre as [métricas no Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Para configurar alertas sem o [Azure Log Analytics](../log-analytics/log-analytics-overview.md), siga estas etapas. Para obter critérios mais avançados de alertas e de ações, [configure o Log Analytics](#azure-diagnostics) também.

1. No menu da folha do aplicativo lógico, em **Monitoramento**, escolha **Diagnóstico** > **Regras de alerta** > **Adicionar alerta**, conforme mostrado aqui:

   ![Adicionar um alerta ao aplicativo lógico](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Na folha **Adicionar uma regra de alerta**, crie o alerta, conforme mostrado:

   1. Em **Recurso**, selecione o aplicativo lógico, caso ainda não esteja selecionado. 
   2. Dê um nome e uma descrição para o alerta.
   3. Selecione uma **Métrica** ou um evento que você deseja acompanhar.
   4. Selecione uma **Condição**, especifique um **Limite** para a métrica e selecione o **Período** de monitoramento dessa métrica.
   5. Selecione se deseja enviar email para o alerta. 
   6. Especifique outro endereço de email para enviar o alerta. 
   Especifique também uma URL de webhook para a qual você deseja enviar o alerta.

   Por exemplo, essa regra envia um alerta quando cinco ou mais execuções falham em uma hora:

   ![Criar uma regra de alerta de métrica](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Para executar um aplicativo lógico em um alerta, você pode incluir o [gatilho de solicitação](../connectors/connectors-native-reqres.md) no fluxo de trabalho, o que permite realizar tarefas como estes exemplos:
> 
> * [Postar no Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar um texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Adicionar uma mensagem a uma fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Configurações e detalhes de eventos do Diagnóstico do Azure

Cada evento de diagnóstico contém detalhes sobre o aplicativo lógico e o evento, por exemplo, o status, a hora de início, a hora de término e assim por diante. Para configurar o monitoramento, o acompanhamento e o log de forma programática, use esses detalhes com a [API REST do Aplicativo Lógico do Azure](https://docs.microsoft.com/rest/api/logic) e a [API REST do Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

Por exemplo, o evento `ActionCompleted` tem as propriedades `clientTrackingId` e `trackedProperties`, que podem ser usadas para acompanhamento e monitoramento:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: se não for fornecido, o Azure gerará essa ID automaticamente e correlacionará eventos em uma execução do aplicativo lógico, incluindo os fluxos de trabalho aninhados que são chamados no aplicativo lógico. Especifique essa ID manualmente em um gatilho, passando um cabeçalho `x-ms-client-tracking-id` com o valor de ID personalizado na solicitação de gatilho. Use um gatilho de solicitação, gatilho HTTP ou gatilho de webhook.

* `trackedProperties`: para acompanhar entradas ou saídas em dados de diagnóstico, adicione as propriedades acompanhadas às ações na definição de JSON do aplicativo lógico. As propriedades acompanhadas só podem acompanhar entradas e saídas de uma única ação, mas você pode usar as propriedades `correlation` dos eventos para correlacionar entre as ações em uma execução.

  Para acompanhar uma ou mais propriedades, adicione a seção `trackedProperties` e as propriedades desejadas à definição de ação. Por exemplo, suponha que você deseje acompanhar dados, como uma “ID de pedido” na telemetria:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>Próximas etapas

* [Criar modelos para implantação de aplicativos lógicos e gerenciamento de versão](../logic-apps/logic-apps-create-deploy-template.md)
* [Cenários B2B com o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorar mensagens do B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
---
title: Monitorar aplicativos lógicos usando logs de Azure Monitor
description: Solucionar problemas de aplicativos lógicos Configurando logs de Azure Monitor e coletando dados de diagnóstico para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: 5df596560e97ea9dba38dca4d4ca58e38caabd37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333946"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Configurar logs de Azure Monitor e coletar dados de diagnóstico para aplicativos lógicos do Azure

Para obter informações de depuração mais avançadas sobre seus aplicativos lógicos durante o tempo de execução, você pode configurar e usar [os logs de Azure monitor](../azure-monitor/platform/data-platform-logs.md) para registrar e armazenar informações sobre dados de tempo de execução e eventos, como eventos de gatilho, eventos de execução e eventos de ação em um [espaço de trabalho log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace). [Azure monitor](../azure-monitor/overview.md) ajuda a monitorar seus ambientes locais e de nuvem para que você possa manter a disponibilidade e o desempenho com mais facilidade. Usando logs de Azure Monitor, você pode criar [consultas de log](../azure-monitor/log-query/log-query-overview.md) que ajudam a coletar e examinar essas informações. Você também pode [usar esses dados de diagnóstico com outros serviços do Azure](#extend-data), como o armazenamento do Azure e os hubs de eventos do Azure.

Para configurar o registro em log para seu aplicativo lógico, você pode [habilitar log Analytics quando você cria seu aplicativo lógico](#logging-for-new-logic-apps)ou pode [instalar a solução de gerenciamento de aplicativos lógicos](#install-management-solution) em seu espaço de trabalho log Analytics para aplicativos lógicos existentes. Essa solução fornece informações agregadas para suas execuções de aplicativo lógico e inclui detalhes específicos, como status, tempo de execução, status de reenvio e IDs de correlação. Em seguida, para habilitar o registro em log e criar consultas para essas informações, [configure Azure monitor logs](#set-up-resource-logs).

Este artigo mostra como habilitar Log Analytics em novos aplicativos lógicos e aplicativos lógicos existentes, como instalar e configurar a solução de gerenciamento de aplicativos lógicos e como configurar e criar consultas para logs de Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa de um [espaço de trabalho log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace). Se você não tiver um espaço de trabalho, saiba [como criar um log Analytics espaço de trabalho](../azure-monitor/learn/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Habilitar Log Analytics para novos aplicativos lógicos

Você pode ativar Log Analytics ao criar seu aplicativo lógico.

1. No [portal do Azure](https://portal.azure.com), no painel em que você fornece as informações para criar seu aplicativo lógico, siga estas etapas:

   1. Em **log Analytics**, selecione **ativado**.

   1. Na lista **log Analytics espaço de trabalho** , selecione o espaço de trabalho para o qual você deseja enviar os dados de suas execuções de aplicativo lógico.

      ![Fornecer informações de aplicativo lógico](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Após a conclusão dessa etapa, o Azure criará o aplicativo lógico, que agora está associado ao espaço de trabalho do Log Analytics. Além disso, essa etapa instala automaticamente a solução de gerenciamento de aplicativos lógicos em seu espaço de trabalho.

1. Quando terminar, selecione **Criar**.

1. Depois de executar seu aplicativo lógico, para exibir suas execuções de aplicativo lógico, [continue com estas etapas](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Instalar a solução de Gerenciamento dos Aplicativos Lógicos

Se você ativou Log Analytics quando criou seu aplicativo lógico, ignore esta etapa. Você já tem a solução de gerenciamento de aplicativos lógicos instalada em seu espaço de trabalho Log Analytics.

1. Na caixa de pesquisa do [portal do Azure](https://portal.azure.com), digite `log analytics workspaces` e, em seguida, selecione **log Analytics espaços de trabalho**.

   ![Selecione "espaços de trabalho do Log Analytics"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. Em **espaços de trabalho do log Analytics**, selecione seu espaço de trabalho.

   ![Selecionar o espaço de trabalho do Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. No painel **visão geral** , em introdução **ao log Analytics**  >  **configurar soluções de monitoramento**, selecione **Exibir soluções**.

   ![No painel Visão geral, selecione "Exibir soluções"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Em **visão geral**, selecione **Adicionar**.

   ![No painel Visão geral, Adicionar nova solução](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Depois que o **Marketplace** for aberto, na caixa de pesquisa, insira `logic apps management` e selecione **Gerenciamento de aplicativos lógicos**.

   ![No Marketplace, selecione "gerenciamento de aplicativos lógicos"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. No painel Descrição da solução, selecione **criar**.

   ![Selecione "criar" para adicionar a solução "gerenciamento de aplicativos lógicos"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Examine e confirme o espaço de trabalho Log Analytics onde você deseja instalar a solução e selecione **criar** novamente.

   ![Selecione "criar" para "gerenciamento de aplicativos lógicos"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Depois que o Azure implantar a solução no grupo de recursos do Azure que contém seu espaço de trabalho Log Analytics, a solução aparecerá no painel Resumo do espaço de trabalho.

   ![Painel de resumo do espaço de trabalho](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurar os logs do Azure Monitor

Ao armazenar informações sobre eventos de tempo de execução e dados em [logs de Azure monitor](../azure-monitor/platform/data-platform-logs.md), você pode criar [consultas de log](../azure-monitor/log-query/log-query-overview.md) que ajudam a localizar e examinar essas informações.

> [!NOTE]
> Depois de habilitar as configurações de diagnóstico, os dados de diagnóstico podem não fluir por até 30 minutos para os logs no destino especificado, como Log Analytics, Hub de eventos ou conta de armazenamento. Esse atraso significa que os dados de diagnóstico desse período de tempo talvez não existam para você examinar. Eventos concluídos e [Propriedades rastreadas](#extend-data) podem não aparecer em seu espaço de trabalho de Log Analytics por 10-15 minutos.

1. No [portal do Azure](https://portal.azure.com), encontre e selecione o aplicativo lógico.

1. No menu do aplicativo lógico, em **monitoramento**, selecione **configurações de diagnóstico**  >  **Adicionar configuração de diagnóstico**.

   ![Em "monitoramento", selecione "configurações de diagnóstico" > "adicionar configuração de diagnóstico"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Para criar a configuração, siga estas etapas:

   1. Forneça um nome para a configuração.

   1. Selecione **Enviar para o Log Analytics**.

   1. Para **assinatura**, selecione a assinatura do Azure associada ao seu espaço de trabalho log Analytics.

   1. Para **log Analytics espaço de trabalho**, selecione o espaço de trabalho que você deseja usar.

   1. Em **log**, selecione a categoria **WorkflowRuntime** , que especifica a categoria de evento que você deseja registrar.

   1. Para selecionar todas as métricas, em **métrica**, selecione **biométricas**.

   1. Quando terminar, selecione **Salvar**.

   Por exemplo:

   ![Selecionar o espaço de trabalho do Log Analytics e os dados para registrar em log](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Exibir status de execuções de aplicativo lógico

Depois que o aplicativo lógico for executado, você poderá exibir os dados sobre essas execuções em seu espaço de trabalho do Log Analytics.

1. Na [portal do Azure](https://portal.azure.com), localize e abra seu espaço de trabalho do log Analytics.

1. No menu do espaço de trabalho, selecione **Resumo do espaço de trabalho**  >  **Gerenciamento de aplicativos lógicos**.

   ![Status e contagem de execuções do aplicativo lógico](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Se o bloco gerenciamento de aplicativos lógicos não mostrar imediatamente os resultados após uma execução, tente selecionar **Atualizar** ou aguarde um curto período antes de tentar novamente.

   Aqui, as execuções de seu aplicativo lógica são agrupadas por nome ou por status de execução. Esta página também mostra detalhes sobre falhas em ações ou gatilhos para executar o aplicativo lógico.

   ![Resumo do status das execuções de seu aplicativo lógico](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Para exibir todas as execuções para um aplicativo lógico específico ou status, selecione a linha para esse aplicativo lógico ou status.

   Veja um exemplo que mostra todas as execuções de um aplicativo lógico específico:

   ![Exibir as execuções e o status do aplicativo lógico](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   > [!NOTE]
   > A funcionalidade de **reenvio** nesta página não está disponível no momento.

   Para ações em que você [configura as propriedades rastreadas](#extend-data), você também pode exibir essas propriedades selecionando **Exibir** na coluna **Propriedades rastreadas** . Para pesquisar as propriedades rastreadas, use o filtro de coluna.

   ![Exibir propriedades rastreadas para um aplicativo lógico](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. Para filtrar os resultados, você pode executar a filtragem do lado do cliente e do servidor.

   * **Filtro do lado do cliente**: para cada coluna, selecione os filtros desejados, por exemplo:

     ![Exemplo de filtros de coluna](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filtro no lado do servidor**: para selecionar uma janela de tempo específica ou para limitar o número de execuções que aparecem, use o controle de escopo na parte superior da página. Por padrão, apenas 1.000 registros aparecem por vez.

     ![Alterar o período](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Para exibir todas as ações e seus detalhes para uma execução específica, selecione a linha para uma execução de aplicativo lógico.

   Aqui está um exemplo que mostra todas as ações e gatilhos para uma execução de aplicativo lógico específico:

   ![Exibir ações para uma execução de aplicativo lógico](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Enviar dados de diagnóstico para o armazenamento do Azure e hubs de eventos do Azure

Juntamente com os logs de Azure Monitor, você pode estender como usar os dados de diagnóstico do aplicativo lógico com outros serviços do Azure, por exemplo:

* [Arquivar logs de recursos do Azure na conta de armazenamento](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)
* [Transmitir logs da plataforma Azure para os hubs de eventos do Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)

Depois, obtenha o monitoramento em tempo real usando a telemetria e a análise de outros serviços, como o [Stream Analytics do Azure](../stream-analytics/stream-analytics-introduction.md) e o [Power BI](../azure-monitor/platform/powerbi.md). Por exemplo:

* [Transmitir dados dos Hubs de Eventos para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analisar dados de streaming com o Stream Analytics e criar um painel de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Com base nos locais em que você deseja enviar dados de diagnóstico, certifique-se de primeiro [criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) ou [criar um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, você pode selecionar os destinos para os quais deseja enviar esses dados. Os períodos de retenção se aplicam somente quando você usa uma conta de armazenamento.

![Enviar dados para uma conta de armazenamento do Azure ou um hub de eventos](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Azure Monitor eventos de diagnóstico

Cada evento de diagnóstico contém detalhes sobre o aplicativo lógico e o evento, por exemplo, o status, a hora de início, a hora de término e assim por diante. Para configurar o monitoramento, o rastreamento e o log de forma programática, você pode usar essas informações com a [API REST para aplicativos lógicos do Azure](/rest/api/logic) e a [api REST para Azure monitor](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows). Você também pode usar as `clientTrackingId` `trackedProperties` Propriedades e, que aparecem em 

* `clientTrackingId`: se não for fornecido, o Azure gerará essa ID automaticamente e correlacionará eventos em uma execução do aplicativo lógico, incluindo os fluxos de trabalho aninhados que são chamados no aplicativo lógico. Você pode especificar manualmente essa ID em um gatilho passando um `x-ms-client-tracking-id` cabeçalho com seu valor de ID personalizado na solicitação do gatilho. Use um gatilho de solicitação, gatilho HTTP ou gatilho de webhook.

* `trackedProperties`: Para rastrear entradas ou saídas em dados de diagnóstico, você pode adicionar uma `trackedProperties` seção a uma ação usando o designer do aplicativo lógico ou diretamente na definição de JSON do aplicativo lógico. As propriedades acompanhadas só podem acompanhar entradas e saídas de uma única ação, mas você pode usar as propriedades `correlation` dos eventos para correlacionar entre as ações em uma execução. Para rastrear mais de uma propriedade, uma ou mais propriedades, adicione a `trackedProperties` seção e as propriedades que você deseja para a definição de ação.

  Aqui está um exemplo que mostra como a definição de ação de **variável de inicialização** inclui propriedades rastreadas da entrada da ação na qual a entrada é uma matriz, não um registro.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  Este exemplo mostra várias propriedades rastreadas:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Este exemplo mostra como o `ActionCompleted` evento inclui os `clientTrackingId` `trackedProperties` atributos e:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
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
         "workflowId": "<logic-app-workflow-ID>",
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
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Criar consultas de monitoramento e acompanhamento](../logic-apps/create-monitoring-tracking-queries.md)
* [Monitorar mensagens B2B com os logs do Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)

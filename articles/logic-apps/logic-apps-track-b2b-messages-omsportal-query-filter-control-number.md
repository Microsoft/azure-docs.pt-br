---
title: Criar consultas de rastreamento para mensagens B2B nos logs do Azure Monitor - aplicativos lógicos do Azure | Microsoft Docs
description: Crie consultas que rastreiam mensagens AS2, X12 e EDIFACT no Azure Log Analytics para os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846401"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Criar consultas de rastreamento para mensagens B2B nos logs do Azure Monitor para aplicativos lógicos do Azure

Para localizar AS2, X12 ou EDIFACT mensagens que você está rastreando com [registra em log do Azure Monitor](../log-analytics/log-analytics-overview.md), você pode criar consultas que filtram ações com base em critérios específicos. Por exemplo, você pode encontrar mensagens baseado em um número de controle de intercâmbio específico.

> [!NOTE]
> Anteriormente, esta página descrevia as etapas para executar essas tarefas com o OMS (Microsoft Operations Management Suite), que será [desativado em janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), substituindo essas etapas pelo Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo lógico configurado com o log de diagnósticos. Saiba [como criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md) e [como configurar o log para esse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração configurada com o monitoramento e log. Saiba [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar o monitoramento e log para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se você ainda não o fez [publicar dados de diagnóstico para os logs do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) e [Configure mensagens de rastreamento em logs do Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Crie consultas com filtros

Para encontrar mensagens com base em propriedades ou valores específicos, você pode criar consultas que usam filtros. 

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, encontre "log analytics" e selecione **Log Analytics**.

   ![Selecione Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Em **Log Analytics**, localize e selecione o espaço de trabalho do Log Analytics. 

   ![Selecione o Espaço de Trabalho do Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. No menu da sua área de trabalho, em **Geral**, selecione **Logs (clássico)** ou **Logs**. 

   Este exemplo mostra como usar a exibição clássica de logs. 
   Se você escolher **Exibir logs** na seção **Maximizar sua experiência do Log Analytics**, em **Pesquisar e analisar logs**, você receberá os **logs (visualização clássica)**. 

   ![Visualizar logs clássicos](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. Na caixa de edição da consulta, comece a digitar o nome do campo que você deseja encontrar. Quando você começa a digitar, o editor de consultas mostra as possíveis correspondências e operações que você pode usar. Depois de criar sua consulta, escolha **Executar** ou pressione a tecla Enter.

   Este exemplo procura correspondências no **LogicAppB2B**. 
   Saiba mais sobre [como encontrar dados nos logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md).

   ![Começar a digitar a cadeia de consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Para alterar o período de tempo que você deseja visualizar, no painel esquerdo, selecione na lista de duração ou arraste o controle deslizante. 

   ![Alterar o período de tempo](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Para adicionar um filtro à sua consulta, escolha **Adicionar**. 

   ![Adicionar um filtro à consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. Em **Adicionar filtros**, digite o nome do filtro que você deseja encontrar. Se você encontrar o filtro, selecione esse filtro. No painel esquerdo, escolha **Adicionar** novamente.

   Por exemplo, aqui está uma consulta diferente que pesquisa eventos **Tipo == "AzureDiagnostics"** e localiza os resultados com base no número de controle de troca selecionando o filtro **event_record_messageProperties_interchangeControlNumber_s**.

   ![Selecionar o valor do filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Depois que você escolher **Adicionar**, sua consulta será atualizada com o evento e o valor de filtro selecionados. 
   Os resultados anteriores agora são filtrados também. 

   Por exemplo, essa consulta pesquisa **Type == "AzureDiagnostics"** e localiza os resultados com base em um número de controle de troca usando o filtro **event_record_messageProperties_interchangeControlNumber_s**.

   ![Resultados filtrados](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Salvar consulta

Para salvar sua consulta no modo de exibição **Logs (clássico)**, siga estas etapas:

1. Na consulta, na **Logs (clássico)** , escolha **Analytics**. 

   ![Escolha "Analytics"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Na barra de ferramentas da consulta, escolha **Salvar**.

   ![Escolha “Salvar”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Forneça os detalhes sobre sua consulta, por exemplo, nomeie sua consulta, selecione **Consulta** e forneça um nome de categoria. Quando terminar, escolha **Salvar**.

   ![Escolha “Salvar”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Para visualizar as consultas salvas, volte para a página de consulta. Na barra de ferramentas da consulta, escolha **Pesquisas salvas**.

   ![Escolha "Pesquisas salvas"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Em **Pesquisas Salvas**, selecione sua consulta para visualizar os resultados. 

   ![Selecionar a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Para atualizar a consulta, de modo que você possa encontrar resultados diferentes, edite-a.

## <a name="find-and-run-saved-queries"></a>Localizar e executar consultas salvas

1. No [portal do Azure](https://portal.azure.com), selecione **Todos os serviços**. Na caixa de pesquisa, encontre "log analytics" e selecione **Log Analytics**.

   ![Selecione Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Em **Log Analytics**, localize e selecione o espaço de trabalho do Log Analytics. 

   ![Selecione o Espaço de Trabalho do Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. No menu da sua área de trabalho, em **Geral**, selecione **Logs (clássico)** ou **Logs**. 

   Este exemplo mostra como usar a exibição clássica de logs. 

1. Depois que a página de consulta for aberta, na barra de ferramentas da consulta, escolha **Pesquisas salvas**.

   ![Escolha "Pesquisas salvas"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Em **Pesquisas Salvas**, selecione sua consulta para visualizar os resultados. 

   ![Selecionar a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   A consulta é executada automaticamente, mas se a consulta não for executada por algum motivo, no editor de consultas, escolha **Executar**.

## <a name="next-steps"></a>Próximas etapas

* [Esquemas de acompanhamento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de acompanhamento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
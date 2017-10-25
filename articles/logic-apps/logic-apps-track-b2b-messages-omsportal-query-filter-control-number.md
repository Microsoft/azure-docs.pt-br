---
title: "Consulta de mensagens B2B no Operations Management Suite – Aplicativo Lógico do Azure | Microsoft Docs"
description: Criar consultas para acompanhar mensagens AS2, X12 e EDIFACT no Operations Management Suite
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 2748d3d3daf7c13dca05f663a4a088598e1b3605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>Consultar mensagens AS2, X12 e EDIFACT no Microsoft OMS (Operations Management Suite)

Para encontrar as mensagens AS2, X12 e EDIFACT que você está acompanhando com o [Azure Log Analytics](../log-analytics/log-analytics-overview.md) no [OMS (Operations Management Suite)](../operations-management-suite/operations-management-suite-overview.md), crie consultas que filtram ações com base em critérios específicos. Por exemplo, você pode encontrar mensagens baseado em um número de controle de intercâmbio específico.

## <a name="requirements"></a>Requisitos

* Um aplicativo lógico configurado com o log de diagnósticos. Saiba [como criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) e [como configurar o log para esse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Uma conta de integração configurada com o monitoramento e log. Saiba [como criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [como configurar o monitoramento e log para essa conta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se você ainda não fez isso, [publique dados de diagnóstico no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) e [configure o acompanhamento de mensagens no OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Depois de atender os requisitos anteriores, você deverá ter um espaço de trabalho no [OMS (Operations Management Suite)](../operations-management-suite/operations-management-suite-overview.md). Você deve usar o mesmo espaço de trabalho do OMS para acompanhar a comunicação B2B no OMS. 
>  
> Se você não tiver um espaço de trabalho do OMS, saiba [como criar um espaço de trabalho do OMS](../log-analytics/log-analytics-get-started.md).

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>Criar consultas de mensagens com filtros no portal do Operations Management Suite

Este exemplo mostra como você pode encontrar mensagens com base no número de controle de intercâmbio.

> [!TIP] 
> Se você souber o nome do espaço de trabalho do OMS, acesse a home page do espaço de trabalho (`https://{your-workspace-name}.portal.mms.microsoft.com`) e comece na etapa 4. Caso contrário, comece na etapa 1.

1. No [portal do Azure](https://portal.azure.com), escolha **Mais serviços**. Pesquise “log analytics” e, em seguida, escolha **Log Analytics**, conforme mostrado aqui:

   ![Encontrar o Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. Em **Log Analytics**, encontre e selecione o espaço de trabalho do OMS.

   ![Selecionar o espaço de trabalho do OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. Em **Gerenciamento**, escolha **Portal do OMS**.

   ![Escolher o portal do OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. Na home page do OMS, escolha **Pesquisa de Logs**.

   ![Na home page do OMS, escolha “Pesquisa de Logs”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -ou-

   ![No menu do OMS, escolha “Pesquisa de Logs”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. Na caixa de pesquisa, insira um campo que você deseja encontrar e, em seguida, pressione **Enter**. Quando você começa a digitar, o OMS mostra as possíveis correspondências e operações que podem ser usadas. Saiba mais sobre [como encontrar dados no Log Analytics](../log-analytics/log-analytics-log-searches.md).

   Este exemplo pesquisa eventos com **Type=AzureDiagnostics**.

   ![Começar a digitar a cadeia de consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. Na barra à esquerda, escolha o período de tempo que você deseja exibir. Para adicionar um filtro à consulta, escolha **+Adicionar**.

   ![Adicionar um filtro à consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. Em **Adicionar Filtros**, insira o nome do filtro para encontrar o filtro desejado. Selecione o filtro e escolha **+Adicionar**.

   Para encontrar o número de controle de intercâmbio, este exemplo pesquisa a palavra “intercâmbio” e seleciona **event_record_messageProperties_interchangeControlNumber_s** como o filtro.

   ![Selecionar filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. Na barra à esquerda, selecione o valor de filtro que você deseja usar e escolha **Aplicar**.

   Este exemplo seleciona o número de controle de intercâmbio para as mensagens desejadas.

   ![Selecionar o valor do filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. Agora, retorne à consulta que está sendo criada. A consulta foi atualizada com o evento de filtro e o valor selecionado. Os resultados anteriores agora são filtrados também.

    ![Retornar à consulta com os resultados filtrados](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Salvar a consulta para uso futuro

1. Na consulta, na página **Pesquisa de Logs**, escolha **Salvar**. Dê um nome à consulta, selecione uma categoria e escolha **Salvar**.

   ![Dê um nome à consulta e uma categoria](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Para exibir a consulta, escolha **Favoritos**.

   ![Escolher “Favoritos”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. Em **Pesquisas Salvas**, selecione a consulta, de modo que você possa exibir os resultados. Para atualizar a consulta, de modo que você possa encontrar resultados diferentes, edite-a.

   ![Selecionar a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>Encontrar e executar as consultas salvas no portal do Operations Management Suite

1. Abra a home page do espaço de trabalho do OMS (`https://{your-workspace-name}.portal.mms.microsoft.com`) e escolha **Pesquisa de Logs**.

   ![Na home page do OMS, escolha “Pesquisa de Logs”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -ou-

   ![No menu do OMS, escolha “Pesquisa de Logs”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Na home page da **Pesquisa de Logs**, escolha **Favoritos**.

   ![Escolher “Favoritos”](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. Em **Pesquisas Salvas**, selecione a consulta, de modo que você possa exibir os resultados. Para atualizar a consulta, de modo que você possa encontrar resultados diferentes, edite-a.

   ![Selecionar a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Próximas etapas

* [Esquemas de acompanhamento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de acompanhamento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
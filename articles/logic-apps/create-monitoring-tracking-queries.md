---
title: Exibir e criar consultas para aplicativos lógicos em logs de Azure Monitor
description: Exibir e criar consultas em logs de Azure Monitor para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: d1f575d9d588ccd99523c6b6d5d2ceba6369dc3d
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309923"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Exibir e criar consultas para monitoramento e acompanhamento em logs de Azure Monitor para aplicativos lógicos do Azure

Você pode exibir as consultas subjacentes que produzem os resultados de [logs de Azure monitor](../azure-monitor/log-query/log-query-overview.md) e criar consultas que filtram os resultados com base em seus critérios específicos. Por exemplo, você pode encontrar mensagens baseado em um número de controle de intercâmbio específico. As consultas usam a [linguagem de consulta Kusto](/azure/data-explorer/kusto/query/), que você pode editar se quiser exibir resultados diferentes. Para obter mais informações, consulte [Azure monitor log queries](/azure/data-explorer/kusto/query/).

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, saiba[como criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Um aplicativo lógico que é configurado com Azure Monitor registro em log e envia essas informações para um espaço de trabalho do Log Analytics. Saiba [como configurar os logs de Azure monitor para seu aplicativo lógico](../logic-apps/monitor-logic-apps.md).

* Se você estiver usando uma conta de integração, verifique se configurou a conta com Azure Monitor registro em log para enviar essas informações a um espaço de trabalho do Log Analytics. Saiba como [Configurar o log de Azure monitor para sua conta de integração](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Exibir consultas por trás dos resultados

Para exibir ou editar a consulta que produz os resultados no resumo do espaço de trabalho, siga estas etapas:

1. Em qualquer página de resultados, na parte inferior, selecione **ver tudo**.

   ![Ver todos os resultados](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   A página logs é aberta e mostra a consulta por trás da página resultados anteriores.

   ![Página de logs – modo de exibição de consulta](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Na página **logs** , você pode selecionar estas opções:

   * Para exibir os resultados da consulta como uma tabela, no editor de consultas, selecione **tabela**.

   * Para alterar a consulta, atualize a cadeia de caracteres de consulta e selecione **executar** para exibir os resultados na tabela.

## <a name="create-your-own-query"></a>Criar sua própria consulta

Para localizar ou filtrar resultados com base em Propriedades ou valores específicos, você pode criar sua própria consulta iniciando a partir de uma consulta vazia ou usar uma consulta existente. Para obter mais informações, consulte Introdução [às consultas de log em Azure monitor](../azure-monitor/log-query/get-started-queries.md).

1. Na [portal do Azure](https://portal.azure.com), localize e selecione seu espaço de trabalho do log Analytics.

1. No menu do espaço de trabalho, em **geral**, selecione **logs**.

1. Inicie de uma consulta vazia ou de quaisquer consultas existentes disponíveis.

   * Para verificar se alguma consulta existente está disponível, na barra de ferramentas de consulta, **Samples queries**selecione o  >  **histórico**de consultas de exemplos, que mostra consultas de execuções de consulta anteriores ou selecione **Gerenciador**de consultas, que mostra consultas predefinidas.

     Por exemplo, a solução Aplicativos Lógicos B2B fornece essas consultas predefinidas:

     ![Comece com as consultas predefinidas da solução "Aplicativos Lógicos B2B"](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Para iniciar de uma consulta vazia, no editor de consultas, comece a digitar a [linguagem de consulta Kusto](/azure/data-explorer/kusto/query/) para sua consulta.

     ![Iniciar com consulta vazia](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Próximas etapas

* [Esquemas de acompanhamento do AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de acompanhamento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
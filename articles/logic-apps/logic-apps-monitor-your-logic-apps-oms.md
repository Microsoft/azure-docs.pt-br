---
title: "Monitorar e obter informações sobre as execuções de seu aplicativo lógico usando o OMS – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Monitorar as execuções de seu aplicativo lógico com o Log Analytics e o OMS (Operations Management Suite) para obter informações ideias e detalhes de depuração mais avançados para solução de problemas e diagnóstico"
author: divyaswarnkar
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/9/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 5afe01e713516277abc3c2dfcccfc6d36cfc70d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-operations-management-suite-oms-and-log-analytics"></a>Monitorar e obter informações sobre execuções de aplicativo lógico com o OMS (Operations Management Suite) e o Log Analytics

Para obter informações monitoramento e depuração mais detalhadas, ative o Log Analytics durante a criação de um aplicativo lógico. O Log Analytics fornece o registro em log de diagnóstico e o monitoramento de suas execuções de aplicativo lógico por meio do portal do OMS (Operations Management Suite). Quando você adiciona a solução de Gerenciamento de Aplicativos Lógicos ao OMS, você obtém o status agregado das execuções de seu aplicativo lógico e detalhes específicos, como status, tempo de execução, status de reenvio e IDs de correlação.

Este tópico mostra como ativar o Log Analytics ou instalar a solução de Gerenciamento de Aplicativos Lógicos no OMS, para que você possa exibir eventos de tempo de execução e dados para a execução de seu aplicativo lógico.

 > [!TIP]
 > Para monitorar seus aplicativos lógicos existentes, execute estas etapas para [ativar o registro em log de diagnóstico e enviar dados de tempo de execução do aplicativo lógica para o OMS](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Requisitos

Antes de começar, você precisa ter um espaço de trabalho do OMS. Saiba [como criar um espaço de trabalho do OMS](../log-analytics/log-analytics-get-started.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Ativar o registro em log de diagnóstico durante a criação de aplicativos lógicos

1. No [Portal do Azure](https://portal.azure.com), crie um aplicativo lógico. Escolha **Novo** > **Enterprise Integration** > **Aplicativo Lógico** > **Criar**.

   ![Criar um aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. Na página **Criar aplicativo lógico**, execute essas tarefas, conforme mostrado:

   1. Dê um nome para seu aplicativo lógico e selecione sua assinatura do Azure. 
   2. Crie ou selecione um grupo de recursos do Azure.
   3. Defina **Log Analytics** como **Ativado**. 
   Selecione o espaço de trabalho do OMS para o qual você deseja enviar dados para suas execuções de aplicativo lógico. 
   4. Quando você estiver pronto, escolha **Fixar no painel** > **Criar**.

      ![Criar aplicativo lógico](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Após a conclusão dessa etapa, o Azure criará seu aplicativo lógico, que agora está associado ao seu espaço de trabalho do OMS. 
      Além disso, essa etapa também instala automaticamente a solução de Gerenciamento de Aplicativos Lógicos em seu espaço de trabalho do OMS.

3. Para exibir as execuções de seu aplicativo lógico no OMS, [continue com estas etapas](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution-in-oms"></a>Instalar a solução de Gerenciamento de Aplicativos Lógicos no OMS

Se você já ativou o Log Analytics durante a criação de seu aplicativo lógico, ignore esta etapa. Você já tem a solução de Gerenciamento de Aplicativos Lógicos instalada no OMS.

1. No [portal do Azure](https://portal.azure.com), escolha **Mais serviços**. Pesquise por "log analytics" enquanto filtra e, em seguida, escolha **Log Analytics**, conforme exibido:

   ![Escolher “Log Analytics”](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. Em **Log Analytics**, encontre e selecione o espaço de trabalho do OMS. 

   ![Selecionar o espaço de trabalho do OMS](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. Em **Gerenciamento**, escolha **Portal do OMS**.

   ![Escolher “Portal do OMS”](media/logic-apps-monitor-your-logic-apps-oms/oms-portal-page.png)

4. Na home page do OMS, se a faixa de atualização for exibida, escolha a faixa para que você atualizar seu espaço de trabalho do OMS primeiro. Então escolha **Galeria de Soluções**.

   ![Escolha "Galeria de Soluções"](media/logic-apps-monitor-your-logic-apps-oms/solutions-gallery.png)

5. Em **Todas as soluções**, localize e escolha o bloco para a solução **Gerenciamento de Aplicativos Lógicos**.

   ![Escolha "Gerenciamento de Aplicativos Lógicos"](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-management-tile2.png)

6. Para instalar a solução em seu espaço de trabalho do OMS, escolha **Adicionar**.

   ![Escolha "Adicionar" para "Gerenciamento de Aplicativos Lógicos"](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-oms-workspace"></a>Exibir as execuções de seu aplicativo lógico em seu espaço de trabalho do OMS

1. Para exibir a contagem e o status das execuções de seu aplicativo lógico, acesse a página de visão geral de seu espaço de trabalho do OMS. Examine os detalhes no bloco **Gerenciamento de Aplicativos Lógicos**.

   ![Bloco de visão geral mostrando a contagem e o status da execução do aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   > [!Note]
   > Se esse cabeçalho de atualização é exibida em vez do bloco de lógica de gerenciamento de aplicativos, escolha a faixa para que você atualizar seu espaço de trabalho do OMS primeiro.
  
   > ![Atualizar "Espaço de Trabalho do OMS"](media/logic-apps-monitor-your-logic-apps-oms/oms-upgrade-banner.png)

2. Para exibir um resumo com mais detalhes sobre as execuções de seu aplicativo lógico, escolha o bloco **Gerenciamento de Aplicativos Lógicos**.

   Aqui, as execuções de seu aplicativo lógica são agrupadas por nome ou por status de execução.

   ![Resumo do status das execuções de seu aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. Para exibir todas as execuções de um aplicativo lógico específico ou status, selecione a linha de um aplicativo lógico ou um status.

   Veja um exemplo que mostra todas as execuções de um aplicativo lógico específico:

   ![Exibir execuções de um aplicativo lógico ou um status](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   > [!NOTE]
   > A coluna **Reenvio** mostra "Sim" para execuções resultantes de uma execução reenviada.

4. Para filtrar esses resultados, execute a filtragem tanto no lado do cliente quanto no lado do servidor.

   * Filtro no lado do cliente: para cada coluna, escolha os filtros desejados. 
   Estes são alguns exemplos:

     ![Exemplo de filtros de coluna](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * Filtro no lado do servidor: para escolher um período específico, ou para limitar o número de execuções exibidas, use o controle de escopo na parte superior da página. 
   Por padrão, apenas 1.000 registros aparecem por vez. 
   
     ![Alterar o período](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. Para exibir todas as ações e os detalhes de uma execução específica, selecione uma linha, o que abrirá a página Pesquisa de Log. 

   * Para exibir essas informações em uma tabela, escolha **Tabela**.
   * Para alterar a consulta, edite a cadeia de consulta na barra de pesquisa. 
   Para ter uma experiência melhor, escolha **Análise Avançada**.

     ![Exibir ações e detalhes da execução de um aplicativo lógico](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)

     Aqui na página do Azure Log Analytics, você pode atualizar consultas e exibir os resultados da tabela. 
     Essa consulta usa a [linguagem de consulta Kusto](https://docs.loganalytics.io/docs/Language-Reference), que pode ser editada se você quiser exibir resultados diferentes. 

     ![Azure Log Analytics – modo de exibição de consulta](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar mensagens do B2B](../logic-apps/logic-apps-monitor-b2b-message.md)

---
title: Monitorar status, exibir histórico e configurar alertas
description: Solucionar problemas de aplicativos lógicos verificando o status da execução, examinando o histórico do gatilho e habilitando alertas em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 3c3d1930234c178a56227830ef0702450ddf4a8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "100580667"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Monitorar o status de execução, examinar o histórico de gatilho e configurar alertas para Aplicativos Lógicos do Azure

Depois de [criar e executar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md), você pode verificar o status de execução do aplicativo lógico, o [histórico de execuções](#review-runs-history), o [histórico de gatilho](#review-trigger-history)e o desempenho. Para obter notificações sobre falhas ou outros possíveis problemas, configure [alertas](#add-azure-alerts). Por exemplo, você pode criar um alerta que detecta “quando mais de cinco execuções falham em uma hora”.

Para monitoramento de eventos em tempo real e depuração mais avançada, configure o log de diagnóstico para seu aplicativo lógico usando [logs de Azure monitor](../azure-monitor/overview.md). Esse serviço do Azure ajuda a monitorar seus ambientes locais e de nuvem para que você possa manter a disponibilidade e o desempenho com mais facilidade. Em seguida, você pode localizar e exibir eventos, como eventos de gatilho, eventos de execução e eventos de ação. Ao armazenar essas informações em [logs de Azure monitor](../azure-monitor/logs/data-platform-logs.md), você pode criar [consultas de log](../azure-monitor/logs/log-query-overview.md) que ajudam a localizar e analisar essas informações. Você também pode usar esses dados de diagnóstico com outros serviços do Azure, como o armazenamento do Azure e os hubs de eventos do Azure. Para obter mais informações, consulte [monitorar aplicativos lógicos usando Azure monitor](../logic-apps/monitor-logic-apps-log-analytics.md).

> [!NOTE]
> Se seus aplicativos lógicos forem executados em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) que foi criado para usar um [ponto de extremidade de acesso interno](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access), você poderá exibir e acessar entradas e saídas do histórico de execuções do aplicativo lógico *somente de dentro de sua rede virtual*. Verifique se você tem conectividade de rede entre os pontos de extremidade privados e o computador do qual você deseja acessar o histórico de execuções. Por exemplo, o computador cliente pode existir dentro da rede virtual do ISE ou dentro de uma rede virtual conectada à rede virtual do ISE, por exemplo, por meio de emparelhamento ou de uma rede virtual privada. Para obter mais informações, confira [Acesso do ponto de extremidade do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Examinar o histórico de execuções

Cada vez que o gatilho é acionado para um item ou evento, o mecanismo de aplicativos lógicos cria e executa uma instância de fluxo de trabalho separada para cada item ou evento. Por padrão, cada instância de fluxo de trabalho é executada em paralelo para que nenhum fluxo de trabalho precise aguardar antes de iniciar uma execução. Você pode examinar o que aconteceu durante essa execução, incluindo o status de cada etapa no fluxo de trabalho, além das entradas e saídas de cada etapa.

1. No [portal do Azure](https://portal.azure.com), encontre e abra seu aplicativo lógico no Designer de Aplicativo Lógico.

   Para localizar seu aplicativo lógico, na caixa principal do Azure Search, digite `logic apps` e, em seguida, selecione **aplicativos lógicos**.

   ![Localizar e selecionar o serviço "aplicativos lógicos"](./media/monitor-logic-apps/find-your-logic-app.png)

   O portal do Azure mostra todos os aplicativos lógicos associados às suas assinaturas do Azure. Você pode filtrar essa lista com base no nome, na assinatura, no grupo de recursos, no local e assim por diante.

   ![Exibir aplicativos lógicos associados a assinaturas](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecione seu aplicativo lógico e, em seguida, selecione **visão geral**.

   No painel Visão geral, em **histórico de execuções**, todas as execuções anteriores, atuais e em espera para seu aplicativo lógico são exibidas. Se a lista mostrar muitas execuções e você não conseguir encontrar a entrada desejada, tente filtrar a lista.

   > [!TIP]
   > Se o status de execução não for exibido, tente atualizar a página Visão geral selecionando **Atualizar**. Não acontece nenhuma execução para um gatilho que é ignorado devido a critérios não cumpridos ou não encontrar dados.

   ![Visão geral, histórico de execuções e outras informações de aplicativo lógico](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Estes são os status de execução possíveis:

   | Status da execução | Descrição |
   |------------|-------------|
   | **Anulado** | A execução foi interrompida ou não foi concluída devido a problemas externos, por exemplo, uma interrupção do sistema ou uma assinatura do Azure enlapsada. |
   | **Cancelar** | A execução foi disparada e iniciada, mas recebeu uma solicitação de cancelamento. |
   | **Falha** | Falha em pelo menos uma ação na execução. Nenhuma ação subsequente no fluxo de trabalho foi configurada para lidar com a falha. |
   | **Executando** | A execução foi disparada e está em andamento, mas esse status também pode aparecer para uma execução que é limitada devido a [limites de ação](logic-apps-limits-and-config.md) ou ao plano de [preços atual](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Dica**: se você configurar o [log de diagnóstico](monitor-logic-apps-log-analytics.md), poderá obter informações sobre os eventos de restrição que acontecerem. |
   | **Êxito** | A execução foi bem-sucedida. Se alguma ação falhar, uma ação subsequente no fluxo de trabalho tratou dessa falha. |
   | **Tempo limite atingido** | A execução atingiu o tempo limite porque a duração atual excedeu o limite de duração da execução, que é controlado pela [configuração **retenção do histórico de execuções em dias**](logic-apps-limits-and-config.md#run-duration-retention-limits). A duração de uma execução é calculada usando a hora de início e o limite de duração da execução na hora de início. <p><p>**Observação**: se a duração da execução também exceder o *limite de retenção do histórico de execução* atual, que também é controlado pela [configuração retenção do **histórico de execução em dias**](logic-apps-limits-and-config.md#run-duration-retention-limits), a execução será desmarcada do histórico de execuções por um trabalho de limpeza diário. Se a execução atingir o tempo limite ou for concluída, o período de retenção sempre será calculado usando a hora de início e o limite de retenção *atual* da execução. Portanto, se você reduzir o limite de duração de uma execução em andamento, a execução atingirá o tempo limite. No entanto, a execução permanece ou é desmarcada do histórico de execuções com base em se a duração da execução excedeu o limite de retenção. |
   | **Aguardando** | A execução não foi iniciada ou está em pausa, por exemplo, devido a uma instância de fluxo de trabalho anterior que ainda está em execução. |
   |||

1. Para examinar as etapas e outras informações de uma execução específica, em **histórico de execuções**, selecione essa execução.

   ![Selecione uma execução específica para examinar](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   O painel **execução do aplicativo lógico** mostra cada etapa na execução selecionada, o status de execução de cada etapa e o tempo necessário para cada etapa ser executada, por exemplo:

   ![Cada ação na execução específica](./media/monitor-logic-apps/logic-app-run-pane.png)

   Para exibir essas informações em formulário de lista, na barra de ferramentas de **execução do aplicativo lógico** , selecione **detalhes da execução**.

   ![Na barra de ferramentas, selecione "detalhes da execução"](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   O modo de exibição detalhes de execução mostra cada etapa, seu status e outras informações.

   ![Examine os detalhes sobre cada etapa na execução](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Por exemplo, você pode obter a propriedade de **ID de correlação** da execução, que pode ser necessária ao usar a [API REST para aplicativos lógicos](/rest/api/logic).

1. Para obter mais informações sobre uma etapa específica, selecione uma das opções:

   * No painel de **execução do aplicativo lógico** , selecione a etapa para que a forma se expanda. Agora você pode exibir informações como entradas, saídas e erros que ocorreram nessa etapa, por exemplo:

     ![No painel execução do aplicativo lógico, exiba a etapa com falha](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * No painel de **detalhes de execução do aplicativo lógico** , selecione a etapa desejada.

     ![No painel de detalhes da execução, exiba a etapa com falha](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Agora você pode exibir informações como entradas e saídas para essa etapa, por exemplo:

   > [!NOTE]
   > Todos os eventos e detalhes de runtime são criptografados no serviço Aplicativos Lógicos. Eles são descriptografados apenas quando um usuário solicita a exibição desses dados. Você pode [ocultar entradas e saídas no histórico de execução](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) ou controlar o acesso do usuário a essas informações usando o [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Examinar histórico de gatilho

Cada execução de aplicativo lógico começa com um gatilho. O histórico de gatilho lista todas as tentativas de gatilho que seu aplicativo lógico fez e informações sobre as entradas e saídas para cada tentativa de gatilho.

1. No [portal do Azure](https://portal.azure.com), encontre e abra seu aplicativo lógico no Designer de Aplicativo Lógico.

   Para localizar seu aplicativo lógico, na caixa principal do Azure Search, digite `logic apps` e, em seguida, selecione **aplicativos lógicos**.

   ![Localizar e selecionar o serviço "aplicativos lógicos"](./media/monitor-logic-apps/find-your-logic-app.png)

   O portal do Azure mostra todos os aplicativos lógicos associados às suas assinaturas do Azure. Você pode filtrar essa lista com base no nome, na assinatura, no grupo de recursos, no local e assim por diante.

   ![Exibir aplicativos lógicos associados a assinaturas](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecione seu aplicativo lógico e, em seguida, selecione **visão geral**.

1. No menu do aplicativo lógico, selecione **visão geral**. Na seção **Resumo** , em **avaliação**, selecione **Ver Histórico de gatilho**.

   ![Exibir o histórico de gatilho para seu aplicativo lógico](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   O painel Histórico de gatilho mostra todas as tentativas de gatilho que seu aplicativo lógico fez. Cada vez que o gatilho é acionado para um item ou evento, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico separada que executa o fluxo de trabalho. Por padrão, cada instância é executada em paralelo para que nenhum fluxo de trabalho precise esperar para iniciar uma execução. Portanto, se o aplicativo lógico for disparado em vários itens ao mesmo tempo, uma entrada de gatilho com a mesma data e hora será exibida para cada item.

   ![Várias tentativas de gatilho para itens diferentes](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Aqui estão os status de possíveis tentativas de gatilho:

   | Status do gatilho | Descrição |
   |----------------|-------------|
   | **Falha** | Ocorreu um erro. Para examinar as mensagens de erro geradas para um disparador com falha, selecione essa tentativa de disparo e escolha **Saídas**. Por exemplo, você pode descobrir entradas que não são válidas. |
   | **Ignorado** | O gatilho verificou o ponto de extremidade, mas não encontrou dados que atendem aos critérios especificados. |
   | **Êxito** | O gatilho verificou o ponto de extremidade e encontrou dados disponíveis. Normalmente, um status **acionado** também aparece junto com esse status. Caso contrário, a definição do gatilho pode ter uma condição ou comando `SplitOn` que não foi atendido. <p><p>Esse status pode se aplicar a um gatilho manual, gatilho recorrente ou gatilho de sondagem. Um gatilho pode ser executado com êxito, mas a execução em si ainda pode falhar quando as ações gerarem erros sem tratamento. |
   |||

   > [!TIP]
   > Você pode verificar novamente o gatilho sem aguardar a próxima recorrência. Na barra de ferramentas visão geral, selecione **executar gatilho** e selecione o gatilho, que força uma verificação. Ou então, selecione **Executar** na barra de ferramentas do Designer de Aplicativos Lógicos.

1. Para exibir informações sobre uma tentativa de gatilho específica, no painel de gatilho, selecione esse evento de gatilho. Se a lista mostrar muitas tentativas de gatilho e você não conseguir encontrar a entrada desejada, tente filtrar a lista. Se você não encontrar os dados esperados, experimente selecionar **Atualizar** na barra de ferramentas.

   ![Exibir tentativa de gatilho específica](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Agora você pode examinar informações sobre o evento de gatilho selecionado, por exemplo:

   ![Exibir informações de gatilho específicas](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Configurar alertas de monitoramento

Para obter alertas com base em métricas específicas ou limites excedidos para seu aplicativo lógico, configure [alertas no Azure monitor](../azure-monitor/alerts/alerts-overview.md). Saiba mais sobre as [métricas no Azure](../azure-monitor/data-platform.md). Para configurar alertas sem usar [Azure monitor](../azure-monitor/logs/log-query-overview.md), siga estas etapas.

1. No menu do aplicativo lógico, em **monitoramento**, selecione **alertas**  >  **nova regra de alerta**.

   ![Adicionar um alerta ao aplicativo lógico](./media/monitor-logic-apps/add-new-alert-rule.png)

1. No painel **criar regra** , em **recurso**, selecione seu aplicativo lógico, se ainda não estiver selecionado. Em **condição**, selecione **Adicionar** para que você possa definir a condição que dispara o alerta.

   ![Adicionar uma condição para a regra](./media/monitor-logic-apps/add-condition-for-rule.png)

1. No painel **Configurar lógica de sinal** , localize e selecione o sinal para o qual você deseja obter um alerta. Você pode usar a caixa de pesquisa ou para classificar os sinais em ordem alfabética, selecionar o cabeçalho da coluna **nome do sinal** .

   Por exemplo, se você quiser enviar um alerta quando um gatilho falhar, siga estas etapas:

   1. Na coluna **nome do sinal** , localize e selecione o sinal dos **gatilhos com falha** .

      ![Selecionar sinal para criar alerta](./media/monitor-logic-apps/find-and-select-signal.png)

   1. No painel de informações que é aberto para o sinal selecionado, em **lógica de alerta**, configure sua condição, por exemplo:

   1. Para **operador**, selecione **maior ou igual a**.

   1. Para **tipo de agregação**, selecione **contagem**.

   1. Para **valor de limite**, insira `1` .

   1. Em **Visualização da condição**, confirme se sua condição aparece correta.

   1. Em **avaliado com base em**, configure o intervalo e a frequência para executar a regra de alerta. Para **granularidade de agregação (período)**, selecione o período para agrupar os dados. Para **frequência de avaliação**, selecione com que frequência você deseja verificar a condição.

   1. Quando estiver pronto, selecione **concluído**.

   Aqui está a condição concluída:

   ![Configurar condição para alerta](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   A página **criar regra** agora mostra a condição que você criou e o custo para executar esse alerta.

   ![Novo alerta na página "criar regra"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Especifique um nome, uma descrição opcional e um nível de severidade para seu alerta. Deixe a configuração **habilitar regra ao criar** ativada ou desative até que você esteja pronto para habilitar a regra.

1. Quando terminar, selecione **criar regra de alerta**.

> [!TIP]
> Para executar um aplicativo lógico em um alerta, você pode incluir o [gatilho de solicitação](../connectors/connectors-native-reqres.md) no fluxo de trabalho, o que permite realizar tarefas como estes exemplos:
> 
> * [Postar no Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Enviar um texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Adicionar uma mensagem a uma fila](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar aplicativos lógicos usando o Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)

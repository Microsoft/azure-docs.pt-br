---
title: Criar alertas do log de atividades para laboratórios no Azure DevTest Labs
description: Este artigo fornece etapas para criar alertas do log de atividades para o laboratório no Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: d5886ea26ddbeb07efc23d61d3197860620eebf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90526350"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Criar alertas do log de atividades para laboratórios no Azure DevTest Labs
Este artigo explica como criar alertas do log de atividades para laboratórios no Azure DevTest Labs (por exemplo: quando uma VM é criada ou quando uma VM é excluída).

## <a name="create-alerts"></a>Criar alertas
Neste exemplo, você cria um alerta para todas as operações administrativas em um laboratório com uma ação que envia um email para os proprietários da assinatura. 

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na barra de pesquisa do portal do Azure, digite **Monitor**e, em seguida, selecione **Monitor** na lista de resultados. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Pesquisar por monitor":::        
1. Selecione **alertas** no menu à esquerda e, em seguida, selecione **nova regra de alerta** na barra de ferramentas. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Pesquisar por monitor":::    
1. Na página **criar regra de alerta** , clique em **selecionar recurso**. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Pesquisar por monitor":::        
1. Selecione **DevTest Labs** para **Filtrar por tipo de recurso**, selecione o laboratório na lista e, em seguida, selecione **concluído**.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Pesquisar por monitor":::
1. De volta à página **criar regra de alerta** , clique em **Selecionar condição**. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Pesquisar por monitor":::    
1. Na página **Configurar lógica de sinal** , selecione um sinal com suporte pelo DevTest Labs. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Pesquisar por monitor":::
1. Filtrar por **nível de evento** (detalhado, informativo, aviso, erro, crítico, tudo), **status** (falha, iniciado, bem-sucedido) e **quem iniciou** o evento. 
1. Selecione **concluído** para concluir a configuração da condição. 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Pesquisar por monitor":::
1. Você especificou para o escopo (laboratório) e a condição para o alerta. Agora, você precisa especificar um grupo de ação com ações a serem executadas quando a condição for atendida. De volta à página **criar regra de alerta** , escolha **selecionar grupo de ação**. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Pesquisar por monitor":::
1. Selecione o link **Criar grupo de ações** na barra de ferramentas. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Pesquisar por monitor":::
1. Na página **Adicionar grupo de ações** , siga estas etapas:
    1. Insira um **nome** para o grupo de ações.
    1. Insira um **nome curto** para o grupo de ações. 
    1. Selecione o **grupo de recursos** no qual você deseja que o alerta seja criado. 
    1. Insira um **nome para a ação**. 
    1. Selecione o **tipo de ação** (neste exemplo, **Azure Resource Manager função de email**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Pesquisar por monitor":::
    1. Na página **função de Azure Resource Manager de email** , selecione a função. Neste exemplo, é **proprietário**. Depois, selecione **OK**. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Pesquisar por monitor":::            
    1. Selecione **OK** na página **Adicionar grupo de ações**. 
1. Agora, na página **criar regra de alerta** , insira um nome para a regra de alerta e, em seguida, selecione **OK**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Pesquisar por monitor":::

## <a name="view-alerts"></a>Exibir alertas 
1. Você verá alertas sobre os **alertas** para todas as operações administrativas (neste exemplo). Os alertas podem demorar algum tempo para serem exibidos. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="Pesquisar por monitor":::
1. Se você selecionar número em uma coluna (por exemplo: **total de alertas**), verá os alertas que foram gerados. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Pesquisar por monitor":::
1. Se você selecionar um alerta, verá detalhes sobre ele. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Pesquisar por monitor":::
1. Neste exemplo, você também receberá um email com conteúdo, conforme mostrado no exemplo a seguir: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="Pesquisar por monitor":::

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre como criar grupos de ações usando diferentes tipos de ação, consulte [criar e gerenciar grupos de ações no portal do Azure](../azure-monitor/platform/action-groups.md).
- Para saber mais sobre os logs de atividade, consulte  [log de atividades do Azure](../azure-monitor/platform/activity-log.md).
- Para saber mais sobre como definir alertas em logs de atividade, consulte [alertas no log de atividades](../azure-monitor/platform/activity-log-alerts.md).


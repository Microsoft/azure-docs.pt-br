---
title: Definir alertas para as métricas da grade de eventos do Azure e operações de log de atividades
description: Este artigo descreve como criar alertas em operações de log de atividades e métricas da grade de eventos do Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 615ebef749be49822a09470eb0d47d8eb31b9a5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119133"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Definir alertas em logs de atividades e métricas da grade de eventos do Azure
Este artigo descreve como criar alertas em operações de log de atividades e métricas da grade de eventos do Azure. Você pode criar alertas em métricas de publicação e de entrega para recursos da grade de eventos do Azure (tópicos e domínios). Para tópicos do sistema, [crie alertas usando a página **métricas** ](#create-alerts-using-the-metrics-page).

## <a name="create-alerts-on-dead-lettered-events"></a>Criar alertas em eventos com mensagens mortas
O procedimento a seguir mostra como criar um alerta sobre a métrica de **eventos de mensagens mortas** para um tópico personalizado. Neste exemplo, um email é enviado para o proprietário do grupo de recursos do Azure quando a contagem de eventos de mensagens mortas para um tópico é acima de 10. 

1. Na página de **tópico da grade de eventos** do tópico, selecione **alertas** no menu à esquerda e, em seguida, selecione **+ nova regra de alerta**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Página alertas – Botão de nova regra de alerta":::
2. Na página **criar regra de alerta** , verifique se o tópico está selecionado para o recurso. Em seguida, clique em **Selecionar condição**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Página alertas – Botão de nova regra de alerta":::    
3. Na página **Configurar lógica de sinal** , siga estas etapas:
    1. Selecione uma métrica ou uma entrada do log de atividades. Neste exemplo, os **eventos de mensagens mortas** são selecionados. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Página alertas – Botão de nova regra de alerta":::        
    2. Selecione dimensões (opcional). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Página alertas – Botão de nova regra de alerta":::        

        > [!NOTE]
        > Você pode selecionar **+** o botão para **EventSubscriptionName** para especificar um nome de assinatura de evento para filtrar eventos. 
    3. Role para baixo. Na seção **lógica de alerta** , selecione um **operador**, **tipo de agregação**e insira um **valor de limite**e selecione **concluído**. Neste exemplo, um alerta é disparado quando a contagem total de eventos mortos inativos é maior que 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Página alertas – Botão de nova regra de alerta":::                
4. De volta à página **criar regra de alerta** , clique em **selecionar grupo de ações**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Página alertas – Botão de nova regra de alerta":::
5. Selecione **Criar grupo de ações** na barra de ferramentas para criar um novo grupo de ações. Você também pode selecionar um grupo de ação existente.        
6. Na página **Adicionar grupo de ações** , siga estas etapas:
    1. Insira um **nome para o grupo de ações**.
    1. Insira um **nome curto** para o grupo de ações.
    1. Selecione uma **assinatura do Azure** na qual você deseja criar o grupo de ações.
    1. Selecione o **grupo de recursos do Azure** no qual você deseja criar o grupo de ações.
    1. Insira um **nome para a ação**. 
    1. Selecione o **tipo de ação**. Neste exemplo, a **função de Azure Resource Manager de email** está selecionada, especificamente a função **proprietários** . 
    1. Selecione **OK** para fechar a página. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Página alertas – Botão de nova regra de alerta":::                   
7. De volta à página **criar regra de alerta** , insira um nome para a regra de alerta e, em seguida, selecione **criar regra de alerta**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Página alertas – Botão de nova regra de alerta":::  
8. Agora, na página **alertas** do tópico, você verá um link para gerenciar regras de alerta se ainda não houver alertas. Se houver alertas, selecione **regras de alerta de gerente** na barra de ferramentas.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Página alertas – Botão de nova regra de alerta":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>Criar alertas em outras métricas ou operações de log de atividades
A seção anterior mostrou como criar alertas em eventos com mensagens mortas. As etapas para criar alertas em outras métricas ou operações de log de atividades são semelhantes. 

Por exemplo, para criar um alerta em um evento de falha de entrega, selecione **eventos de falha de entrega** na página **Configurar lógica de sinal** . 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="Página alertas – Botão de nova regra de alerta":::


## <a name="create-alerts-using-the-metrics-page"></a>Criar alertas usando a página métricas
Você também pode criar alertas usando a página **métricas** . As etapas são semelhantes. Para tópicos do sistema, você só pode usar a página **métricas** para criar alertas, pois a página **alertas** não está disponível. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Página alertas – Botão de nova regra de alerta":::   
    

> [!NOTE]
> Este artigo não abrange todas as diferentes etapas e combinações que você pode usar para criar um alerta. Para obter uma visão geral dos alertas, consulte [visão geral de alertas](../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).

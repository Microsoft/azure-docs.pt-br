---
title: Gerenciar notificações de alerta de eventos de dispositivo para seus recursos do Azure Stack Edge pro | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar alertas de eventos de dispositivo em seus recursos do Azure Stack Edge pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 585343137a4a8fd8a1fb591c640e1183d71c0fd3
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443090"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Gerenciar notificações de alerta de eventos de dispositivo nos recursos do Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como criar regras de ação no portal do Azure para disparar ou suprimir notificações de alerta para eventos de dispositivo que ocorrem em um grupo de recursos, uma assinatura do Azure ou um recurso individual do Azure Stack Edge. Este artigo se aplica a todos os modelos do Azure Stack Edge.  

## <a name="about-action-rules"></a>Sobre regras de ação

Uma regra de ação pode disparar ou suprimir notificações de alerta. A regra de ação é adicionada a um *grupo de ações* – um conjunto de preferências de notificação que é usado para notificar os usuários que precisam agir em alertas disparados em diferentes contextos para um recurso ou conjunto de recursos.

Para obter mais informações sobre regras de ação, consulte [Configurando uma regra de ação](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule). Para obter mais informações sobre grupos de ações, consulte [criar e gerenciar grupos de ações no portal do Azure](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> O recurso de regras de ação está em versão prévia. Algumas telas e etapas podem mudar à medida que o processo é refinado.


## <a name="create-an-action-rule"></a>Criar uma regra de ação

Execute as seguintes etapas no portal do Azure para criar uma regra de ação para seu dispositivo Azure Stack Edge.

> [!NOTE]
> Essas etapas criam uma regra de ação que envia notificações para um grupo de ações. Para obter detalhes sobre como criar uma regra de ação para suprimir notificações, consulte [Configurando uma regra de ação](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

1. Vá para o dispositivo Azure Stack Edge no portal do Azure e vá para **monitoramento > alertas**. Selecione **Gerenciar ações**.

   ![Monitorando alertas, gerenciar exibição de ações](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. Selecione **regras de ação (versão prévia)** e, em seguida, selecione **+ nova regra de ação**.

   ![Opção Gerenciar ações, regras de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. Na tela **criar regra de ação** , use **escopo** para selecionar uma assinatura do Azure, um grupo de recursos ou um recurso de destino. A regra de ação atuará em todos os alertas gerados dentro desse escopo.

   1. Escolha **selecionar** por **escopo**.

      ![Selecionar um escopo para uma nova regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. Selecione a **assinatura** para a regra de ação, opcionalmente filtre por um tipo de **recurso** . Para filtrar Azure Stack recursos do Edge, selecione **dispositivos data Box Edge (dataBoxEdge)**.

      A área de **recursos** lista os recursos disponíveis com base em suas seleções.
  
      ![Recursos disponíveis na tela selecionar escopo](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. Marque a caixa de seleção de cada recurso ao qual você deseja aplicar a regra. Você pode selecionar a assinatura, os grupos de recursos ou os recursos individuais. Em seguida, selecione **Concluído**.

      ![Configurações de exemplo para um escopo de regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      A tela **criar regra de ação** mostra o escopo selecionado.

      ![Escopo concluído para uma regra de ação de Azure Stack borda](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. Use opções de **filtro** para restringir o aplicativo da regra para o subconjunto de alertas dentro do escopo selecionado.

   1. Selecione **Adicionar** para abrir o painel **adicionar filtros** .

      ![Opção para adicionar filtros a uma regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. Em **filtros**, adicione cada filtro que você deseja aplicar. Para cada filtro, selecione o tipo de filtro, o **operador** e o **valor**.
   
      Para obter uma lista de opções de filtro, consulte [critérios de filtro](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria).

      Os filtros de exemplo a seguir se aplicam a todos os alertas nos níveis de severidade 2, 3 e 4 que o serviço monitor gera para Azure Stack recursos de borda.

      Quando terminar de adicionar filtros, selecione **concluído**.
   
      ![Filtro de exemplo para uma regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. Na tela **criar regra de ação** , selecione **grupo de ações** para criar uma regra que envia notificações. Em seguida, por **ações**, escolha **selecionar**.

   ![Opção de grupo de ação para criar uma regra de ação que envia notificações](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > Para criar uma regra que suprime notificações, você deve escolher **supressão**. Para obter mais informações, consulte [Configurando uma regra de ação](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule).

6. Selecione o grupo de ações que você deseja usar com esta regra de ação. Em seguida, escolha **Selecionar**. Sua nova regra de ação será adicionada às preferências de notificação do grupo de ações selecionado.

   Se você precisar criar um novo grupo de ação, selecione **+ Criar grupo de ação** e siga as etapas em [criar um grupo de ação usando o portal do Azure](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal).

   ![Selecione um grupo de ações para usar com a regra e escolha Selecionar.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. Dê um **nome** e uma **Descrição** à nova regra de ação e atribua a regra a um grupo de recursos.

9. A nova regra é habilitada por padrão. Se você não quiser começar a usar a regra imediatamente, selecione **não** para **habilitar a criação de atualização de regra**.

10. Ao concluir as configurações, selecione **criar**.

    ![Configurações concluídas para uma regra de ação que enviará notificações de alerta](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    A tela **regras de ação (versão prévia)** é aberta, mas você pode não ver a nova regra de ação imediatamente. O foco é **todos os** grupos de recursos.

11. Para ver sua nova regra de ação, selecione o grupo de recursos para a regra.

    ![Tela de regras de ação com a nova regra exibida](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>Exibir notificações

As notificações saem quando um novo evento dispara um alerta para um recurso que está dentro do escopo de uma regra de ação.

O grupo de ações para um conjunto de regras que recebe uma notificação e o tipo de notificação enviada por email, uma mensagem SMS (Short Message Service) ou ambos.

Pode levar alguns minutos para receber notificações depois que um alerta é disparado.

A notificação por email terá uma aparência semelhante a esta.

![Notificação por email de exemplo para uma regra de ação](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>Próximas etapas

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- Consulte [monitorar seu Azure Stack Edge pro](azure-stack-edge-monitor.md) para obter informações sobre como examinar eventos de dispositivo, status de hardware e gráficos de métricas. 
- Consulte [usando Azure monitor](azure-stack-edge-gpu-enable-azure-monitor.md) para obter informações sobre como otimizar Azure monitor para dispositivos de Azure Stack GPU pro de borda.
- Consulte [criar, exibir e gerenciar alertas de métrica usando Azure monitor destino de link](../azure-monitor/alerts/alerts-metric.md) para obter informações sobre como gerenciar alertas individuais.
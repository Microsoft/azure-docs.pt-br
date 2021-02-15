---
title: Receber alertas do log de atividades nas notificações de serviço do Azure usando portal do Azure
description: Saiba como usar o portal do Azure para configurar alertas do log de atividades para notificações de integridade do serviço usando o portal do Azure.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 88fbdeeb8bdcc239f1591e053aaf4fb1c36b9b19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289779"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Criar alertas do log de atividades em notificações de serviço usando o portal do Azure
## <a name="overview"></a>Visão geral

Este artigo mostra como usar o portal do Azure para configurar alertas do log de atividades para notificações de integridade do serviço usando o portal do Azure.  

As notificações de integridade do serviço são armazenadas no [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md). Considerando o grande volume de informações armazenadas no log de atividades, há uma interface do usuário separada para facilitar a exibição e a configuração de alertas em notificações de integridade do serviço. 

Você pode receber um alerta quando o Azure envia notificações de integridade do serviço para sua assinatura do Azure. Você pode configurar o alerta de acordo com:

- A classe de notificação de integridade do serviço (problemas de serviço, manutenção planejada, comunicados de integridade, avisos de segurança).
- A assinatura afetada.
- Os serviços afetados.
- As regiões afetadas.

> [!NOTE]
> As notificações de integridade do serviço não enviam alertas para eventos do Resource Health.

Também é possível configurar para quem o alerta deve ser enviado:

- Selecione um grupo de ações existente.
- Crie um novo grupo de ações (que pode ser usado posteriormente para futuros alertas).

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

Para saber mais sobre como configurar alertas de notificação de integridade do serviço usando modelos do Azure Resource Manager, consulte [modelos do Resource Manager](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Assista a um vídeo sobre como configurar seu primeiro alerta de integridade do serviço do Azure

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="create-service-health-alert-using-azure-portal"></a>Criar alerta de integridade do serviço usando portal do Azure
1. No [portal](https://portal.azure.com), selecione **Integridade do Serviço**.

    ![O serviço “Integridade do Serviço”](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Na seção **Alertas**, selecione **Alertas de integridade**.

    ![A guia “Alertas de integridade”](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selecione **adicionar alerta de integridade do serviço** e preencha os campos.

    ![O comando “Criar alerta de integridade do serviço”](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecione a **assinatura**, os **Serviços**e as **regiões** para as quais você deseja ser alertado.

    [![A caixa de diálogo "Adicionar alerta do log de atividades"](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)](./media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png#lightbox)

> [!NOTE]
>Esta assinatura é usada para salvar o alerta do log de atividades. O recurso de alerta é implantado para essa assinatura e monitora os eventos no log de atividades para ele.

5. Escolha os **tipos de eventos** para os quais você deseja ser alertado: *problema de serviço*, *manutenção planejada*, comunicados de *integridade*e *consultoria de segurança*.

6. Clique em **selecionar grupo de ações** para escolher um grupo de ações existente ou para criar um novo grupo de ações. Para obter mais informações sobre grupos de ações, consulte [criar e gerenciar grupos de ações no portal do Azure](../azure-monitor/platform/action-groups.md).


7. Defina os detalhes do alerta inserindo um **Nome de regra de alerta** e uma **Descrição**.

8. Selecione o **Grupo de recursos** onde você deseja que o alerta seja salvo.



Em alguns minutos, o alerta estará ativo e começará a disparar com base nas condições especificadas durante a criação.

Saiba como [Configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md). Para saber mais sobre o esquema do webhook para alertas de log de atividades, veja [Webhooks para alertas do log de atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [as práticas recomendadas para configurar alertas de integridade do serviço do Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Saiba como [configurar notificações por push móvel para a integridade do serviço do Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Saiba mais sobre as [notificações de integridade do serviço](service-notifications.md).
- Saiba mais sobre a [limitação da taxa de notificação](../azure-monitor/platform/alerts-rate-limiting.md).
- Examine o [esquema de webhook de alerta do log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas do log de atividades](../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.
- Saiba mais sobre [grupos de ação](../azure-monitor/platform/action-groups.md).

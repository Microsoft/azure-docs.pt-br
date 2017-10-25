---
title: "Receber alertas do log de atividades em notificações de serviço | Microsoft Docs"
description: "Seja notificado por SMS, email ou webhook quando um serviço do Azure for executado."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: bf6a98fd7e7e11764bef174f9efd0635fa7efe9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Criar alertas do log de atividades em notificações de serviço
## <a name="overview"></a>Visão geral
Este artigo mostra como configurar alertas do log de atividades para notificações de integridade do serviço usando o portal do Azure.  

Você pode receber um alerta quando o Azure envia notificações de integridade do serviço para sua assinatura do Azure. Você pode configurar o alerta de acordo com:

- A classe de notificação de integridade do serviço (incidente, manutenção, informações etc.).
- Os serviços afetados.
- As regiões afetadas.
- O status da notificação (ativo versus resolvido).
- O nível das notificações (informativo, aviso ou erro).

Também é possível configurar para quem o alerta deve ser enviado:

- Selecione um grupo de ações existente.
- Crie um novo grupo de ações (que pode ser usado posteriormente para futuros alertas).

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](monitoring-action-groups.md).

Para saber mais sobre como configurar alertas de notificação de integridade do serviço usando modelos do Azure Resource Manager, consulte [modelos do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Criar um alerta em uma notificação de integridade do serviço para um novo grupo de ação usando o portal do Azure
1. No [portal](https://portal.azure.com), selecione **Monitor**.

    ![O serviço “Monitor”](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2. Na seção **Log de atividades**, selecione **Alertas**.

    ![A guia “Alertas”](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

3. Selecione **Adicionar alerta do log de atividades** e preencha os campos.

    ![O comando "Adicionar alerta do log de atividades"](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

4. Insira um nome na caixa **Nome do log de atividades alerta** e forneça uma **Descrição**.

    ![A caixa de diálogo "Adicionar alerta do log de atividades"](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

5. A caixa **Assinatura** é automaticamente preenchida com a sua assinatura atual. Esta assinatura é usada para salvar o alerta do log de atividades. O recurso de alerta é implantado para essa assinatura e monitora os eventos no log de atividades para ele.

6. Selecione o **Grupo de recursos** no qual o recurso de alerta é criado. Este não é o grupo de recursos monitorado pelo alerta. Em vez disso, é o grupo de recursos onde se encontra o recurso de alerta.

7. Na caixa **Categoria de evento**, selecione **Integridade do Serviço**. Opcionalmente, selecione as notificações **Serviço**, **Região**, **Tipo**, **Status** e **Nível** de integridade do serviço que você deseja receber.

8. Em **Alerta via**, selecione o botão **Novo** grupo de ações. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é referenciado nas notificações enviadas quando esse alerta é acionado.

9. Defina uma lista de destinatários fornecendo os seguintes itens do destinatário:

    a. **Nome**: nome, alias ou identificador do destinatário.

    b. **Tipo de Ação**: selecione SMS, email ou webhook.

    c. **Detalhes**: de acordo com o tipo de ação escolhido, insira um número de telefone, endereço de email ou URI de webhook.

10. Selecione **OK** para criar o alerta.

Em alguns minutos, o alerta estará ativo e começará a disparar com base nas condições especificadas durante a criação.

Para saber mais sobre o esquema do webhook para alertas de log de atividades, veja [Webhooks para alertas do log de atividades do Azure](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ações definido nessas etapas é reutilizável, como um grupo de ação existente, para todas as definições de alerta futuras.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Criar um alerta em uma notificação de integridade do serviço para um grupo de ação existente usando o portal do Azure

1. Siga as etapas 1 a 7 na seção anterior para criar a notificação de integridade do serviço. 

2. Em **Alerta via**, selecione o botão Grupo de ações **existente**. Selecione o grupo de ação apropriado.

3. Selecione **OK** para criar o alerta.

Em alguns minutos, o alerta estará ativo e começará a disparar com base nas condições especificadas durante a criação.

## <a name="manage-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, ele ficará visível na seção **Alertas** da folha **Monitor**. Selecione o alerta que você deseja gerenciar:

* Edite-o.
* Exclua-o.
* Desabilite-o ou habilite-o, se desejar interromper temporariamente ou continuar recebendo notificações do alerta.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [notificações de integridade do serviço](monitoring-service-notifications.md).
- Saiba mais sobre [limitação de taxa de notificação](monitoring-alerts-rate-limiting.md).
- Examine o [esquema do webhook de alertas de log de atividades](monitoring-activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas. 
- Saiba mais sobre [grupos de ação](monitoring-action-groups.md).

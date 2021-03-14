---
title: Criar Resource Health alertas usando o portal do Azure
description: Crie um alerta usando portal do Azure que o notifique quando os recursos do Azure ficarem indisponíveis.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: e48c400e5be3516b08496db7a4cb6a19e45d6c97
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594632"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Configurar alertas do Resource Health usando o portal do Azure

Este artigo mostra como configurar alertas do log de atividades para notificações do Resource Health usando o portal do Azure.

O Azure Resource Health mantém você informado sobre o status de integridade atual e histórico de seus recursos do Azure. Os alertas do Azure Resource Health podem notificar você quase em tempo real quando esses recursos tiverem uma alteração no seu status de integridade. Criar alertas do Resource Health por meio de programação permite que usuários criem e personalizem alertas em massa.

As notificações do Resource Health são armazenadas no [log de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md) , considerando o volume possivelmente grande de informações armazenadas no log de atividades, há uma interface do usuário separada para facilitar a exibição e a configuração de alertas em notificações do Resource Health.
Você pode receber um alerta quando o recurso do Azure envia notificações de integridade de recursos para sua assinatura do Azure. Você pode configurar o alerta de acordo com:

* A assinatura afetada.
* Os tipos de recurso foram afetados.
* Os grupos de recursos afetados.
* Os recursos afetados.
* Os status do evento do (s) recurso (s) afetados.
* Os recursos afetados do (s).
* Os tipos de motivos do (s) recurso (ões) afetados (s).

Também é possível configurar para quem o alerta deve ser enviado:

* Selecione um grupo de ações existente.
* Crie um novo grupo de ações (que pode ser usado posteriormente para futuros alertas).

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/alerts/action-groups.md).

Para obter informações sobre como configurar alertas de notificação do Resource Health usando modelos de Azure Resource Manager, consulte [modelos do Resource Manager](./resource-health-alert-arm-template-guide.md).
Resource Health alerta usando portal do Azure

## <a name="resource-health-alert-using-azure-portal"></a>Resource Health alerta usando o portal do Azure

1. No [portal](https://portal.azure.com/)do Azure, selecione **integridade do serviço**.

    ![Seleção de integridade do serviço](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. Na seção **Resource Health** , selecione **integridade do serviço**.
3. Selecione **adicionar alerta de integridade de recurso** e preencha os campos.
4. Em destino do alerta, selecione a **assinatura**, os **tipos de recursos**, os grupos de **recursos** e o **recurso** para o qual você deseja ser alertado.

    ![Seleção de seleção de destino](./media/resource-health-alert-monitor-guide/alert-target.png)

5. Em condição de alerta, selecione:
    1. O **status do evento** para o qual você deseja ser alertado. O nível de severidade do evento: ativo, resolvido, em andamento, atualizado
    2. O **status do recurso** para o qual você deseja ser alertado. O status do recurso do evento: disponível, indisponível, desconhecido, degradado
    3. O **tipo de motivo** para o qual você deseja ser alertado. A causa do evento: plataforma iniciada, seleção de integridade da seleção de condição de alerta iniciada pelo usuário ![](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. Em Definir detalhes de alerta, forneça os seguintes detalhes:
    1. **Nome da regra de alerta**: o nome da nova regra de alerta.
    2. **Descrição**: a descrição para a nova regra de alerta.
    3. **Salvar o alerta para o grupo de recursos**: selecione o grupo de recursos no qual deseja salvar essa nova regra.
7. Em **Grupo de ações**, no menu suspenso, especifique o grupo de ações que deseja atribuir à nova regra de alerta. Ou [crie um grupo de ações](../azure-monitor/alerts/action-groups.md) e atribua à nova regra. Para criar um novo grupo, selecione + **novo grupo**.
8. Para habilitar as regras após criá-lo, selecione **Sim** da opção **Habilitar regra após a criação**.
9. Selecione **Criar regra de alerta**.

A regra de alerta para o log de atividades é criada e uma mensagem de confirmação aparece no canto superior direito da janela.
É possível habilitar, desabilitar, editar ou excluir uma regra. Saiba mais sobre [como gerenciar regras de log de atividades](../azure-monitor/alerts/alerts-activity-log.md#view-and-manage-in-the-azure-portal).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Resource Health:

* [Visão geral de Azure Resource Health](Resource-health-overview.md)
* [Tipos de recursos e verificações de integridade disponíveis por meio de Azure Resource Health](resource-health-checks-resource-types.md)

Criar alertas de Integridade do Serviço:

* [Configurar alertas para a Integridade do Serviço](./alerts-activity-log-service-notifications-portal.md) 
* [Esquema de eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log-schema.md)
* [Configurar alertas de integridade de recursos do Azure usando modelos do Resource Manager](./resource-health-alert-arm-template-guide.md)

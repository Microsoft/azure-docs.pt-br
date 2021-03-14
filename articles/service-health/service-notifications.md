---
title: Exibir as notificações de integridade do serviço usando o Portal do Azure
description: Exiba suas notificações de integridade do serviço no portal do Azure. As notificações de integridade do serviço são publicadas pela infraestrutura do Azure no log de atividades do Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 9f9f3e7b10d9aa0014e4e00e7bfa72c9dc66e142
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587999"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Exibir as notificações de integridade do serviço usando o Portal do Azure

As notificações de integridade do serviço são publicadas pela infraestrutura do Azure no [log de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md).  As notificações contêm informações sobre os recursos em sua assinatura. Considerando o volume possivelmente grande de informações armazenadas no log de atividades, há uma interface do usuário separada para facilitar a exibição e a configuração de alertas nas notificações de integridade do serviço. 

As notificações de integridade do serviço podem ser informativas ou acionáveis, dependendo da classe.

Para obter mais informações sobre as várias classes de notificações de integridade do serviço, consulte [Propriedades de notificações de integridade do serviço](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Exibindo as notificações de integridade do serviço no Portal do Azure

1. Na [portal do Azure](https://portal.azure.com), selecione **Monitor**.

    ![Captura de tela do menu do Portal do Azure, com a opção Monitor selecionada](./media/service-notifications/home-monitor.png)

    Este Azure Monitor reúne todas as suas configurações e dados de monitoramento em uma exibição consolidada. Ela abre primeiro na seção **Log de atividades** .

1. Selecione **Alertas**.

    ![Captura do log de atividades do Monitor, com a opção Alertas selecionada](./media/service-notifications/service-health-summary.png)

1. Selecione **+Adicionar alerta do log de atividades** e configure um alerta para garantir que você receba notificações de serviço futuras. Para obter mais informações, consulte [Criar alertas do log de atividades em notificações de serviço](./alerts-activity-log-service-notifications-portal.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [alertas do log de atividades](../azure-monitor/alerts/activity-log-alerts.md).

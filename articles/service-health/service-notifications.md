---
title: Exibir as notificações de integridade do serviço usando o Portal do Azure
description: As notificações de integridade do serviço permitem exibir mensagens de integridade do serviço publicadas pelo Microsoft Azure.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538348"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Exibir as notificações de integridade do serviço usando o Portal do Azure

Notificações de integridade do serviço são publicadas pela infraestrutura do Azure para o [log de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md).  As notificações contêm informações sobre os recursos em sua assinatura. Devido ao volume possivelmente grande de informações armazenadas no log de atividades, há uma interface de usuário separadas para torná-lo mais fácil exibir e configurar alertas em notificações de integridade do serviço. 

As notificações de integridade do serviço podem ser informativas ou acionáveis, dependendo da classe.

Para obter mais informações sobre as várias classes de notificações de integridade do serviço, consulte [as propriedades de notificações de integridade do serviço](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Exibindo as notificações de integridade do serviço no Portal do Azure

1. No [portal do Azure](https://portal.azure.com), selecione **Monitor**.

    ![Captura de tela do menu do Portal do Azure, com a opção Monitor selecionada](./media/service-notifications/home-monitor.png)

    Este Azure Monitor reúne todas as suas configurações e dados de monitoramento em uma exibição consolidada. Ela abre primeiro na seção **Log de atividades** .

1. Selecione **Alertas**.

    ![Captura do log de atividades do Monitor, com a opção Alertas selecionada](./media/service-notifications/service-health-summary.png)

1. Selecione **+Adicionar alerta do log de atividades** e configure um alerta para garantir que você receba notificações de serviço futuras. Para obter mais informações, consulte [Criar alertas do log de atividades em notificações de serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [alertas do log de atividades](../azure-monitor/platform/activity-log-alerts.md).

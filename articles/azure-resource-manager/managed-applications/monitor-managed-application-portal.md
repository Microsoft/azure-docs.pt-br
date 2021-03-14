---
title: Usar portal do Azure para monitorar um aplicativo gerenciado
description: Mostra como usar o portal do Azure para monitorar disponibilidade e alertas para um aplicativo gerenciado.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: a02062edd1a940bcc6588ab53457e0af91fedd9a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578278"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorar uma instância implantada de um aplicativo gerenciado

Depois de implantar um aplicativo gerenciado em sua assinatura do Azure, você pode querer verificar o status do aplicativo. Este artigo mostra as opções no portal do Azure para verificar o status. Você pode monitorar a disponibilidade dos recursos em seu aplicativo gerenciado. Você também pode configurar e exibir alertas.

## <a name="view-resource-health"></a>Exibir a integridade dos recursos

1. Selecione sua instância do aplicativo gerenciado.

   ![Selecionar aplicativo gerenciado](./media/monitor-managed-application-portal/select-managed-application.png)

1. Selecione **Resource Health**.

   ![Selecionar integridade de recursos](./media/monitor-managed-application-portal/select-resource-health.png)

1. Exiba a disponibilidade dos recursos em seu aplicativo gerenciado.

   ![Exibir a integridade dos recursos](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Exibir alertas

1. Selecione **Alertas**.

   ![Selecionar alertas](./media/monitor-managed-application-portal/select-alerts.png)

1. Se você tiver regras de alerta configuradas, você verá informações sobre os alertas que foram gerados.

   ![Exibir alertas](./media/monitor-managed-application-portal/view-alerts.png)

1. Para adicionar regras de alerta, selecione **+Nova regra de alerta**.

   ![Criar alerta](./media/monitor-managed-application-portal/create-new-alert.png)

É possível criar alertas para a instância do aplicativo gerenciado ou para os recursos no aplicativo gerenciado. Confira mais informações sobre como criar alertas em [Visão geral dos alertas no Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter exemplos de aplicativo gerenciado, veja [Projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
* Para implantar um aplicativo gerenciado, confira [Implantar aplicativo do catálogo de serviços por meio do portal do Azure](deploy-service-catalog-quickstart.md).
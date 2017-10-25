---
title: "Criar e gerenciar grupos de ações no portal do Azure | Microsoft Docs"
description: "Este artigo mostra como criar e gerenciar grupos de ações no portal do Azure."
author: anirudhcavale
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
ms.date: 05/15/2017
ms.author: ancav
ms.openlocfilehash: 7347be8520e643cd166851d3f525a9a0726b40c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Criar e gerenciar grupos de ações no portal do Azure
## <a name="overview"></a>Visão geral ##
Este artigo mostra como criar e gerenciar grupos de ação no Portal do Azure.

Você pode configurar uma lista de ações com grupos de ações. Então, esses grupos podem ser usados quando você define alertas de log de atividades. Esses grupos podem ser reutilizados por cada alerta do log de atividades que definir, garantindo que as mesmas ações são executadas cada vez que o alerta do log de atividades é disparado.

Um grupo de ação pode ter até 10 ações de cada tipo. Cada ação é composta das seguintes propriedades:

* **Nome:** um identificador exclusivo dentro do grupo de ações.  
* **Tipo de ação**: enviar um SMS, enviar um email, chamar um webhook ou enviar dados para uma ferramenta de ITSM.
* **Detalhes**: o número de telefone correspondente, o endereço de email, o URI de webhook ou os Detalhes de Conexão de ITSM.

Para saber mais sobre como usar modelos do Azure Resource Manager para configurar grupos de ação, veja [Modelos do Resource Manager de grupos de ações](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Como criar um grupo de ações usando o portal do Azure ##
1. No [portal](https://portal.azure.com), selecione **Monitor**. A folha **Monitor** consolida todas as suas configurações e dados em uma exibição de monitoramento.

    ![O serviço “Monitor”](./media/monitoring-action-groups/home-monitor.png)
2. Na seção **Log de atividades**, selecione **Grupos de ações**.

    ![Na guia "Grupos de ações"](./media/monitoring-action-groups/action-groups-blade.png)
3. Selecione **Adicionar grupo de ações** e preencha os campos.

    ![O comando "Adicionar grupo de ações"](./media/monitoring-action-groups/add-action-group.png)
4. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

      ![Caixa de diálogo Adicionar grupo de ações](./media/monitoring-action-groups/action-group-define.png)

5. A caixa **Assinatura** é automaticamente preenchida com a sua assinatura atual. Esta assinatura é aquela na qual o grupo de ação é salvo.

6. Selecione o **Grupo de recursos** no qual o grupo de ações é salvo.

7. Defina uma lista de ações fornecendo estas ações:

    a. **Nome**: insira um identificador exclusivo para esta ação.

    b. **Tipo de Ação**: selecione SMS, email, webhook ou ITSM.

    c. **Detalhes**: de acordo com o tipo de ação, insira um número de telefone, endereço de email, URI de webhook ou os Detalhes de Conexão de ITSM. Para Ação do ITSM, além disso, especifique **Item de Trabalho** e outros campos necessários para a ferramenta de ITSM. 

> [!NOTE]
> Ação de ITSM exige uma Conexão de ITSM. Saiba como criar uma [Conexão de ITSM](../log-analytics/log-analytics-itsmc-overview.md). Atualmente, a Ação de ITSM funciona apenas em Alertas do Log de Atividades. Em outros tipos de alerta, essa ação é inoperante no momento.
>
>

8. Selecione **OK** para criar o grupo de ações.

## <a name="manage-your-action-groups"></a>Gerenciar seus grupos de ação ##
Depois de criar um grupo, ele ficará visível na seção **Grupos de ações** da folha **Monitor**. Selecione o grupo de ações que você deseja gerenciar:

* Adicionar, editar ou remover ações.
* Excluir o grupo de ação.

## <a name="next-steps"></a>Próximas etapas ##
* Saiba mais sobre o [comportamento de alertas por SMS](monitoring-sms-alert-behavior.md).  
* Tenha uma [compreensão do esquema de webhook de alerta do log de atividades](monitoring-activity-log-alerts-webhook.md).  
* Saiba mais sobre o [Conector de ITSM](../log-analytics/log-analytics-itsmc-overview.md)
* Saiba mais sobre a [limitação de taxa](monitoring-alerts-rate-limiting.md) para alertas. 
* Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](monitoring-activity-log-alerts-on-service-notifications.md).

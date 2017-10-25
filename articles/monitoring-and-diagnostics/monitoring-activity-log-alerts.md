---
title: Criar alertas do log de atividades | Microsoft Docs
description: Seja notificado por SMS, webhook e email quando ocorrerem determinados eventos no log de atividades.
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
ms.date: 08/03/2017
ms.author: johnkem
ms.openlocfilehash: 3885469ec0e1fcc31386dd0ad7fe6cb5d03ab28e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-activity-log-alerts"></a>Criar alertas do log de atividades

## <a name="overview"></a>Visão geral
Os alertas de log de atividades são alertas ativados quando ocorre um novo evento de log de atividades que corresponde às condições especificadas no alerta. Eles são recursos do Azure e, portanto, podem ser criados usando um modelo do Azure Resource Manager. Eles também podem ser criados, atualizados ou excluídos no portal do Azure. Este artigo apresenta os conceitos por trás de alertas de log de atividades. Ele então mostra como usar o portal do Azure para configurar um alerta em eventos do log de atividades.

Normalmente, você cria alertas de log de atividade para receber notificações quando:

* As alterações específicas nos recursos de sua assinatura do Azure, normalmente com escopo para recursos ou grupos de recursos específicos. Por exemplo, convém ser notificado quando qualquer máquina virtual em myProductionResourceGroup for excluída. Ou você pode receber uma notificação se quaisquer funções novas forem atribuídas a um usuário em sua assinatura.
* Ocorre um evento de integridade do serviço. Os eventos de integridade de serviço incluem uma notificação de incidentes e eventos de manutenção que se aplicam aos recursos em sua assinatura.

Em ambos os casos, o alerta do log de atividades só monitorará eventos na assinatura na qual o alerta foi criado.

Você pode configurar um alerta do log de atividades com base em qualquer propriedade de nível superior no objeto JSON de um evento do log de atividades. No entanto, o portal mostra as opções mais comuns:

- **Categoria**: Administrativa, Integridade do Serviço, Dimensionamento Automático e Recomendação. Para saber mais, veja [Visão geral do log de atividades](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Para saber mais sobre os eventos de integridade do serviço, veja [Receber alertas do log de atividades em notificações de serviço](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Grupo de recursos**
- **Recurso**
- **Tipo de recurso**
- **Nome da operação**: nome da operação de Controle de Acesso Baseado em Função do Resource Manager.
- **Nível**: o nível de gravidade do evento (Detalhado, Informativo, Aviso, Erro ou Crítico).
- **Status**: o status do evento, normalmente Iniciado, Falha ou Êxito.
- **Evento iniciado por**: também conhecido como o "chamador". O endereço de email ou o identificador do Azure Active Directory do usuário que realizou a operação.

>[!NOTE]
>Você deve especificar pelo menos dois dos critérios anteriores em seu alerta, sendo que um deve ser a categoria. Você não pode criar um alerta que seja ativado sempre que um evento for criado nos logs de atividades.
>
>

Quando um alerta do log de atividades é ativado, ele usa um grupo de ações para gerar ações ou notificações. Um grupo de ações é um conjunto reutilizável de destinatários de notificação, como endereços de email, URLs de webhook ou números de telefone de SMS. Os destinatários podem ser referenciados de vários alertas para centralizar e agrupar seus canais de notificação. Quando você define o alerta do log de atividades, tem duas opções. Você pode:

* Use um grupo existente no seu alerta do log de atividades. 
* Crie um novo grupo de ações. 

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações no portal do Azure](monitoring-action-groups.md).

Para saber mais sobre as notificações de integridade do serviço, veja [Receber alertas do log de atividades sobre as notificações de integridade do serviço](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Criar um alerta em um evento do log de atividades com um novo grupo de ações usando o portal do Azure
1. No [portal](https://portal.azure.com), selecione **Monitor**.

    ![O serviço “Monitor”](./media/monitoring-activity-log-alerts/home-monitor.png)
2. Na seção **Log de atividades**, selecione **Alertas**.

    ![A guia “Alertas”](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. Selecione **Adicionar alerta do log de atividades** e preencha os campos.

4. Insira um nome na caixa **Nome do log de atividades alerta** e selecione uma **Descrição**.

    ![O comando "Adicionar alerta do log de atividades"](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. A caixa **Assinatura** é automaticamente preenchida com a sua assinatura atual. Esta assinatura é aquela na qual o grupo de ação é salvo. O recurso de alerta é implantado nessa assinatura e monitora os eventos do log de atividades dela.

    ![A caixa de diálogo "Adicionar alerta do log de atividades"](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. Selecione o **Grupo de recursos** no qual o recurso de alerta é criado. Esse não é o grupo de recursos monitorado pelo alerta. Em vez disso, é o grupo de recursos onde se encontra o recurso de alerta.

7. Opcionalmente, selecione uma **Categoria de evento** para modificar os filtros adicionais que são mostrados. Para eventos administrativos, os filtros incluem **Grupo de recursos**, **Recurso**, **Ripo de recurso**, **Nome da operação**, **Nível**, **Status** e **Evento iniciado por**. Esses valores identificam quais eventos este alerta deverá monitorar.

    >[!NOTE]
    >Você deve especificar pelo menos um dos critérios anteriores em seu alerta. Você não pode criar um alerta que seja ativado sempre que um evento for criado nos logs de atividades.
    >
    >

8. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

9.  Defina uma lista de ações fornecendo os seguintes dados da ação:

    a. **Nome**: insira o nome, o alias ou o identificador da ação.

    b. **Tipo de Ação**: selecione SMS, email ou webhook.

    c. **Detalhes**: de acordo com o tipo de ação escolhido, insira um número de telefone, um endereço de email ou um URI de webhook.

10. Selecione **OK** para criar o alerta.

O alerta leva alguns minutos para se propagar totalmente e então se tornar ativo. Ele dispara quando novos eventos correspondem aos critérios do alerta.

Para saber mais, veja [Noções básicas sobre o esquema de webhook usado em alertas do log de atividades](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>O grupo de ações definido nessas etapas é reutilizável, como um grupo de ação existente, para todas as definições de alerta futuras.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Criar um alerta em um evento do log de atividades para um grupo de ações existente usando o portal do Azure
1. Siga as etapas 1 a 7 na seção anterior para criar o alerta do log de atividades.

2. Em **Notificar via**, selecione o botão Grupo de ações **existente**. Selecione um grupo de ações existente na lista.

3. Selecione **OK** para criar o alerta.

O alerta leva alguns minutos para se propagar totalmente e então se tornar ativo. Ele dispara quando novos eventos correspondem aos critérios do alerta.

## <a name="manage-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, ele ficará visível na seção Alertas da folha Monitor. Selecione o alerta que você deseja gerenciar:

* Edite-o.
* Exclua-o.
* Desabilite-o ou habilite-o, se desejar interromper temporariamente ou continuar recebendo notificações do alerta.

## <a name="next-steps"></a>Próximas etapas
- Obtenha uma [visão geral dos alertas](monitoring-overview-alerts.md).
- Saiba mais sobre [limitação de taxa de notificação](monitoring-alerts-rate-limiting.md).
- Examine o [esquema do webhook de alertas de log de atividades](monitoring-activity-log-alerts-webhook.md).
- Saiba mais sobre [grupos de ação](monitoring-action-groups.md).  
- Saiba mais sobre as [notificações de integridade do serviço](monitoring-service-notifications.md).
- Crie um [alerta de log de atividades para monitorar todas as operações de mecanismo de dimensionamento automático em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Crie um [alerta de log de atividades para monitorar todas as operações de escalar horizontalmente/reduzir horizontalmente com falha na sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

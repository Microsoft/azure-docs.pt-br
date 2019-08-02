---
title: Alertas de log de atividades no Azure Monitor
description: Seja notificado por SMS, webhook, email e mais quando ocorrerem determinados eventos no log de atividades.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 61b5b96636ea12b5c63da657e006bd3121c34756
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67852602"
---
# <a name="alerts-on-activity-log"></a>Alertas no log de atividades 

## <a name="overview"></a>Visão geral
Alertas do log de atividades são alertas que são ativados quando ocorre um novo [evento do log de atividades](activity-log-schema.md) que corresponde às condições especificadas no alerta. Com base na ordem e no volume dos eventos registrados no [log de atividades do Azure](activity-logs-overview.md), a regra de alerta será acionada. As regras de alerta do log de atividades são recursos do Azure, para que possam ser criadas usando um modelo de Azure Resource Manager. Eles também podem ser criados, atualizados ou excluídos no portal do Azure. Este artigo apresenta os conceitos por trás de alertas de log de atividades. Para obter mais informações sobre a criação ou uso de regras de alerta do log de atividades, consulte [criar e gerenciar alertas do log de atividades](alerts-activity-log.md).

> [!NOTE]
> **Não é possível** criar alertas para eventos na categoria de alerta do log de atividades.

Normalmente, você cria alertas de log de atividade para receber notificações quando:

* As operações específicas ocorrem nos recursos de sua assinatura do Azure, normalmente com escopo para recursos ou grupos de recursos específicos. Por exemplo, convém ser notificado quando qualquer máquina virtual em myProductionResourceGroup for excluída. Ou você pode receber uma notificação se quaisquer funções novas forem atribuídas a um usuário em sua assinatura.
* Ocorre um evento de integridade do serviço. Os eventos de integridade de serviço incluem uma notificação de incidentes e eventos de manutenção que se aplicam aos recursos em sua assinatura.

Uma analogia simples para entender as condições em que as regras de alerta podem ser criadas no log de atividades é explorar ou filtrar eventos por meio [do log de atividades em portal do Azure](activity-log-view.md#azure-portal). No log de atividades Azure Monitor, é possível filtrar ou localizar o evento necessário e, em seguida, criar um alerta usando o botão de **alerta Adicionar log de atividades** .

Em ambos os casos, o alerta do log de atividades só monitorará eventos na assinatura na qual o alerta foi criado.

Você pode configurar um alerta do log de atividades com base em qualquer propriedade de nível superior no objeto JSON de um evento do log de atividades. Para saber mais, veja [Visão geral do log de atividades](./activity-logs-overview.md#categories-in-the-activity-log). Para saber mais sobre os eventos de integridade do serviço, veja [Receber alertas do log de atividades em notificações de serviço](./alerts-activity-log-service-notifications.md). 

Os alertas do log de atividades têm algumas opções comuns:

- **Categoria**: Administrativa, Integridade do Serviço, Dimensionamento Automático, Segurança, Política e Recomendação. 
- **Escopo**: o recurso individual ou um conjunto de recursos para o qual o alerta no log de atividades está definido. O escopo para um alerta do log de atividades pode ser definido em vários níveis:
    - Nível de Recurso: por exemplo, para uma máquina virtual específica
    - Nível do Grupo de Recursos: por exemplo, todas as máquinas virtuais em um grupo de recursos específico
    - Nível de Assinatura: por exemplo, todas as máquinas virtuais em uma assinatura (ou) todos os recursos em uma assinatura
- **Grupo de recursos**: por padrão, a regra de alerta é salva no mesmo grupo de recursos como no destino definido no escopo. O usuário também pode definir o grupo de recursos no qual a regra de alerta deve ser armazenada.
- **Tipo de recurso**: o Resource Manager definido pelo namespace para o destino do alerta.
- **Nome da operação**: O nome da [operação de Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md) utilizado para o controle de acesso baseado em função. As operações não registradas com Azure Resource Manager não podem ser usadas em uma regra de alerta do log de atividades.
- **Nível**: o nível de gravidade do evento (Detalhado, Informativo, Aviso, Erro ou Crítico).
- **Status**: o status do evento, normalmente Iniciado, Falha ou Êxito.
- **Evento iniciado por**: também conhecido como o "chamador". O endereço de email ou o identificador do Azure Active Directory do usuário que realizou a operação.

> [!NOTE]
> Em uma assinatura, é possível criar até 100 regras de alerta para uma atividade de escopo: um único recurso, todos os recursos no grupo de recursos (ou) todo o nível de assinatura.

Quando um alerta do log de atividades é ativado, ele usa um grupo de ações para gerar ações ou notificações. Um grupo de ações é um conjunto reutilizável de destinatários de notificação, como endereços de email, URLs de webhook ou números de telefone de SMS. Os destinatários podem ser referenciados de vários alertas para centralizar e agrupar seus canais de notificação. Quando você define o alerta do log de atividades, tem duas opções. Você pode:

* Use um grupo existente no seu alerta do log de atividades.
* Crie um novo grupo de ações.

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações no portal do Azure](action-groups.md).


## <a name="next-steps"></a>Próximas etapas

- Obtenha uma [visão geral dos alertas](alerts-overview.md).
- Saiba mais sobre [criar e modificar alertas do log de atividades](alerts-activity-log.md).
- Examine o [esquema do webhook de alertas de log de atividades](activity-log-alerts-webhook.md).
- Saiba mais sobre as [notificações de integridade do serviço](service-notifications.md).
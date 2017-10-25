---
title: "Limitação de taxa para SMS, emails e webhooks | Microsoft Docs"
description: "Entenda como o Azure limita o número de notificações possíveis de webhook, email ou SMS de um grupo de ações."
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
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>Taxa de limitação mensagens SMS, emails e postagens de webhook postagens
A limitação de taxa é uma suspensão de notificações que ocorre quando um excesso de notificações é enviado para determinado número de telefone ou endereço de email. A limitação de taxa assegura que os alertas sejam gerenciáveis e acionáveis.

As regras de SMS e Email são iguais. O limite de taxa é:

 - **SMS**: 10 mensagens em uma hora.
 - **Email**: 100 mensagens em uma hora.

## <a name="rate-limit-rules"></a>Regras do limite de taxa
- Um número de telefone ou email específico tem um limite de taxa imposto quando recebe mais mensagens do que o limite permite.
- Um número de telefone ou email pode fazer parte de grupos de ações em várias assinaturas. A limitação de taxa aplica-se a todas as assinaturas. Ela se aplica assim que o limite é atingido, mesmo se as mensagens forem enviadas de várias assinaturas.  
- Quando um número de telefone ou email tem uma limitação de taxa, uma notificação adicional é enviada para comunicar a limitação de taxa. A notificação indica quando a limitação de taxa expira.

## <a name="rate-limit-of-webhooks"></a>Limite de taxa de webhooks ##
Não há nenhuma limitação de taxa em vigor para webhooks.

## <a name="next-steps"></a>Próximas etapas ##
* Saiba mais sobre o [comportamento de alertas por SMS](monitoring-sms-alert-behavior.md).
* Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](monitoring-activity-log-alerts-on-service-notifications.md).

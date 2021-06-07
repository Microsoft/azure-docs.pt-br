---
title: Limitação de taxa para SMS, emails, notificações por push
description: Entenda como o Azure limita o número de notificações possíveis de webhook, email, push do Azure App ou SMS de um grupo de ações.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.openlocfilehash: a0e1c74c19d56e3dd4b6dbb1cb9ababbbb49d062
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037941"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Limitação de taxa para voz, SMS, emails, notificações por push do Azure App e webhooks
A limitação de taxa é uma suspensão de notificações que ocorre quando muitas são enviadas para determinado número de telefone, endereço de email ou dispositivo. A limitação de taxa assegura que os alertas sejam gerenciáveis e acionáveis.

Os limites de taxa são:

- **SMS**: não mais de 1 SMS a cada 5 minutos.
- **Voz**: não mais de 1 chamada de voz a cada 5 minutos.
- **Email**: não mais de 100 emails em uma hora.
 
  Outras ações não são limitadas por classificação.

## <a name="rate-limit-rules"></a>Regras do limite de taxa
- Um número de telefone ou email específico tem um limite de taxa imposto quando recebe mais mensagens do que o limite permite.
- Um número de telefone ou email pode fazer parte de grupos de ações em várias assinaturas. A limitação de taxa aplica-se a todas as assinaturas. Ela se aplica assim que o limite é atingido, mesmo se as mensagens forem enviadas de várias assinaturas.
- Quando um endereço de email tem uma limitação de taxa, uma notificação adicional é enviada para comunicar a limitação de taxa. O email informa quando o limite de taxa expira.

## <a name="next-steps"></a>Próximas etapas ##
* Saiba mais sobre o [comportamento de alertas por SMS](alerts-sms-behavior.md).
* Obtenha uma [visão geral dos alertas do log de atividades](./alerts-overview.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](../../service-health/alerts-activity-log-service-notifications-portal.md).
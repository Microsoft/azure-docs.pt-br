---
title: "Comportamento dos alertas por SMS em grupos de ação | Microsoft Docs"
description: Formato de mensagem SMS e como responder a mensagens SMS para cancelar a assinatura, assinar novamente ou solicitar ajuda.
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
ms.openlocfilehash: 3e4eca174209eeb9cbce1d45111d1e5cc30af8b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sms-alert-behavior-in-action-groups"></a>Comportamento dos alertas por SMS em grupos de ação
## <a name="overview"></a>Visão geral ##
Os grupos de ações permitem configurar uma lista de destinatários. Esses grupos podem, então, ser utilizados ao definir alertas de log de atividades, garantindo que um grupo de ação específico seja notificado quando o alerta de log de atividades for disparado. Um dos mecanismos de alerta com suporte é o SMS; os alertas dão suporte à comunicação bidirecional. Um usuário pode responder a um alerta para:

- **Cancelar a assinatura de alertas:** um usuário pode cancelar a assinatura de todos os alertas por SMS de todos os grupos de ação ou de um grupo de ação individual.  
- **Assinar os alertas novamente:** um usuário pode assinar novamente todos os alertas por SMS de todos os grupos de ação ou de um grupo de ação individual.  
- **Solicitar ajuda:** um usuário pode solicitar mais informações sobre o SMS. Ele será redirecionado para este artigo

Este artigo aborda o comportamento dos alertas por SMS e as ações de resposta que o usuário pode executar de acordo com a localidade do usuário:

## <a name="usacanada-sms-behavior"></a>Comportamento de SMS nos EUA e no Canadá
### <a name="receiving-an-sms-alert"></a>Recebendo um alerta por SMS
Um receptor de SMS, configurado como parte de um grupo de ação, receberá um SMS quando um alerta for acionado. O SMS trará as seguintes informações:
* Nome curto do grupo de ação ao qual esse alerta foi enviado
- Título do alerta

### <a name="unsubscribing-from-sms-alerts-for-one-action-group"></a>Cancelando a assinatura de alertas por SMS de um grupo de ação
Um usuário pode cancelar a assinatura do SMS para alertas de um grupo de ação respondendo ao código curto 20873 com as palavras-chave: “DISABLE &lt;Nome curto do grupo de ação&gt;”.

Ex.: Um usuário que deseja cancelar a assinatura dos alertas de um grupo de ação com o nome curto “Azure” enviará um SMS para o código curto 20873 que indica “DISABLE Azure”

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>Cancelando a assinatura de alertas por SMS de todos os grupos de ação
Um usuário pode cancelar a assinatura de todos os alertas por SMS de todos os grupos de ação respondendo ao código curto 20873 com uma das seguintes palavras-chave:
* STOP

Ex.: Um usuário que deseja cancelar a assinatura de todos os alertas por SMS de todos os grupos de ação enviará um SMS para o código curto 20873 que indica “STOP”

>[!NOTE]
>Se um usuário tiver cancelado a assinatura de alertas por SMS, mas, em seguida, ele for adicionado a um novo grupo de ação, ele receberá alertas por SMS para esse novo grupo de ação, mas a assinatura de todos os grupos de ação anteriores permanecerá cancelada.
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>Assinando novamente alertas por SMS de um grupo de ação
Um usuário pode assinar o SMS novamente para receber alertas de um grupo de ação respondendo ao código curto 20873 com as palavras-chave: “ENABLE &lt;Nome curto do grupo de ação&gt;”.

Ex.: Um usuário que deseja assinar novamente os alertas de um grupo de ação com o nome curto “Azure” enviará um SMS para o código curto 20873 que indica “ENABLE Azure”

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>Assinando novamente alertas por SMS de todos os grupos de ação
Um usuário pode assinar todos os SMS novamente para receber alertas de todos os grupos de ação respondendo ao código curto 20873 com uma das seguintes palavras-chave:

* START

Ex.: Um usuário que deseja cancelar a assinatura de todos os alertas por SMS de todos os grupos de ação enviará um SMS para o código curto 20873 que indica “START”

### <a name="requesting-help-via-sms"></a>Solicitando ajuda por SMS
Um usuário pode solicitar mais informações sobre o SMS que recebeu respondendo ao código curto 20873 com uma das seguintes palavras-chave:
* HELP

Uma resposta será enviada para o usuário com um link para este artigo.

## <a name="next-steps"></a>Próximas etapas
Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas  
Saiba mais sobre a [limitação de taxa de SMS](monitoring-alerts-rate-limiting.md)  
Saiba mais sobre [grupos de ação](monitoring-action-groups.md)

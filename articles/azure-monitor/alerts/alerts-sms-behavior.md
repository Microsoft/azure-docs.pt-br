---
title: Comportamento dos alertas por SMS em grupos de ação
description: Formato de mensagem SMS e como responder a mensagens SMS para cancelar a assinatura, assinar novamente ou solicitar ajuda.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.openlocfilehash: e46517865ba01a0d4d113696fbadabf5ae7b0105
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037924"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Comportamento dos alertas por SMS em grupos de ação

## <a name="overview"></a>Visão geral 
Os grupos de ações permitem configurar uma lista de ações. Esses grupos são usados ao definir alertas, garantindo que um grupo de ação específico seja notificado quando o alerta for disparado. Uma das ações com suporte é o SMS; as notificações de SMS dão suporte à comunicação bidirecional. Um usuário pode responder a um SMS para:

- **Cancelar a assinatura de alertas:** um usuário pode cancelar a assinatura de todos os alertas por SMS de todos os grupos de ação ou de um único grupo de ação.
- **Assinar alertas novamente:** um usuário pode assinar novamente todos os alertas por SMS para todos os grupos de ação ou de um único grupo de ação.  
- **Solicitar ajuda:** um usuário pode solicitar mais informações sobre o SMS. Ele será redirecionado para este artigo.

Este artigo aborda o comportamento dos alertas por SMS e as ações de resposta que o usuário pode executar de acordo com a localidade do usuário:

## <a name="receiving-an-sms-alert"></a>Recebendo um alerta por SMS
Um receptor de SMS configurado como parte de um grupo de ação receberá um SMS quando um alerta for disparado. O SMS contém as seguintes informações:
* Nome curto do grupo de ação ao qual esse alerta foi enviado
* Título do alerta

| REPLY | Descrição |
| ----- | ----------- |
| DISABLE `<Action Group Short name>` | Desabilita o SMS adicional do Grupo de Ação |
| ENABLE `<Action Group Short name>` | Habilita novamente o SMS do Grupo de Ação |
| STOP | Desabilita o SMS adicional de todos os Grupos de Ação |
| START | Habilita novamente o SMS de TODOS os Grupos de Ação |
| HELP | Uma resposta é enviada para o usuário com um link para este artigo. |

>[!NOTE]
>Se um usuário tiver cancelado a assinatura de alertas por SMS, mas, em seguida, ele for adicionado a um novo grupo de ação, ele receberá alertas por SMS para esse novo grupo de ação, mas a assinatura de todos os grupos de ação anteriores permanecerá cancelada.

## <a name="next-steps"></a>Próximas etapas
Obtenha uma [visão geral dos alertas do log de atividades](./alerts-overview.md) e saiba como receber alertas  
Saiba mais sobre a [limitação de taxa de SMS](alerts-rate-limiting.md)  
Saiba mais sobre [grupos de ação](./action-groups.md)
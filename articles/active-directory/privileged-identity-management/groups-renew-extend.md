---
title: Renovar o proprietário do grupo expirado de atribuições de membro no Privileged Identity Management-Azure AD | Microsoft Docs
description: Saiba como estender ou renovar atribuições de grupo de atribuição de função no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87505985"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Estender ou renovar atribuições de grupo de acesso privilegiado (versão prévia) no Privileged Identity Management

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) fornece controles para gerenciar o ciclo de vida de acesso e atribuição para grupos de acesso privilegiado. Os administradores podem atribuir funções usando as propriedades de data e hora de início e término. Quando a extremidade de atribuição se aproxima, Privileged Identity Management envia notificações por email aos usuários ou grupos afetados. Ele também envia notificações por e-mail aos administradores do recurso para garantir que o acesso apropriado seja mantido. As atribuições podem ser renovadas e permanecer visíveis em um estado expirado por até 30 dias, mesmo que o acesso não seja estendido.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar

Somente os administradores do recurso podem estender ou renovar atribuições de grupo de acesso privilegiado. O usuário ou grupo afetado pode solicitar a extensão de atribuições que estão prestes a expirar e a solicitação para renovar as atribuições que já expiraram.

## <a name="when-notifications-are-sent"></a>Quando as notificações são enviadas

Privileged Identity Management envia notificações por email para administradores e usuários afetados de atribuições de grupo de acesso privilegiado que estão expirando:

- Dentro de 14 dias antes da expiração
- Um dia antes da expiração
- Quando uma atribuição expira

Os administradores recebem notificações quando um usuário ou grupo solicita para estender ou renovar uma atribuição expirando ou expirada. Quando um administrador resolve a solicitação, todos os administradores e o usuário solicitante são notificados sobre a aprovação ou negação.

## <a name="extend-group-assignments"></a>Estender atribuições de grupo

As etapas a seguir descrevem o processo de solicitação, resolução ou administração de uma extensão ou renovação de uma atribuição de grupo.

### <a name="self-extend-expiring-assignments"></a>Estender automaticamente as atribuições de expiração

Os usuários atribuídos a um grupo de acesso privilegiado podem estender as atribuições de grupo de expiração diretamente da guia **qualificada** ou **ativa** na página de **atribuições** do grupo. Os usuários ou grupos podem solicitar a extensão de atribuições qualificadas e ativas que expiram nos próximos 14 dias.

![Página minhas funções listando assgnments qualificados com uma coluna de ação](media/groups-renew-extend/self-extend-group-assignment.png)

Quando a data/hora de término da atribuição é dentro de 14 dias, o comando **Extend** está disponível. Para solicitar uma extensão de uma atribuição de grupo, selecione **estender** para abrir o formulário de solicitação.

![Estender o painel de atribuição de grupo com uma caixa de motivo e detalhes](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>Recomendamos incluir os detalhes de por que a extensão é necessária e por quanto tempo a extensão deve ser concedida (se você tiver essa informação).

Em questão de momentos, os administradores recebem uma notificação por email solicitando que eles revisem a solicitação de extensão. Se uma solicitação para estender já tiver sido enviada, uma notificação do Azure aparecerá no Portal.

Para exibir o status ou cancelar sua solicitação, abra a página **solicitações pendentes** para a atribuição de grupo.

![Atribuições de grupo de acesso privilegiado-página solicitações pendentes mostrando o link para cancelar](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Extensão aprovada pelo administrador

Quando um usuário ou grupo envia uma solicitação para estender uma atribuição de grupo, os administradores recebem uma notificação por email que contém os detalhes da atribuição original e o motivo da solicitação. A notificação inclui um link direto com a solicitação para o administrador aprovar ou negar.

Além de usar o link do email a seguir, os administradores podem aprovar ou negar solicitações acessando o portal de administração do Privileged Identity Management e selecionando **aprovar solicitações** no painel esquerdo.

![Atribuições de grupo de acesso privilegiado – aprovar solicitações listando solicitações e links para aprovar ou negar](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

Quando um administrador seleciona **aprovar** ou **negar**, os detalhes da solicitação são mostrados, juntamente com um campo para fornecer uma justificativa de negócios para os logs de auditoria.

![Aprovar solicitação de atribuição de grupo com motivo do solicitante, tipo de atribuição, hora de início, hora de término e motivo](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

Ao aprovar uma solicitação para estender uma atribuição de grupo, os administradores de recursos podem escolher uma nova data de início, data de término e tipo de atribuição. Alterar o tipo de atribuição pode ser necessário se o administrador quiser fornecer acesso limitado para concluir uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de **Qualificado** para **Ativo**. Isso significa que eles podem fornecer acesso ao solicitante sem precisar que eles sejam ativados.

### <a name="admin-initiated-extension"></a>Extensão iniciada pelo administrador

Se um usuário atribuído a um grupo não solicitar uma extensão para a atribuição de grupo, um administrador poderá estender uma atribuição em nome do usuário. As extensões administrativas da atribuição de grupo não exigem aprovação, mas as notificações são enviadas a todos os outros administradores depois que a atribuição é estendida.

Para estender uma atribuição de grupo, navegue até o modo de exibição de atribuição em Privileged Identity Management. Localize a atribuição que requer uma extensão. Em seguida, selecione **estender** na coluna ação.

![Página atribuições listando atribuições de grupo qualificadas com links para estender](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>Renovar atribuições de grupo

Embora seja conceitualmente semelhante ao processo de solicitação de uma extensão, o processo para renovar uma atribuição de grupo expirado é diferente. Usando as etapas, as atribuições e os administradores a seguir podem renovar o acesso a atribuições expiradas quando necessário.

### <a name="self-renew"></a>Renovação automática

Os usuários que não podem mais acessar recursos podem acessar até 30 dias de histórico de atribuição expirado. Para fazer isso, navegue até **minhas funções** no painel esquerdo e selecione a guia **atribuições expiradas** .

![Página minhas funções-guia atribuições expiradas](media/groups-renew-extend/groups-renew-from-my-roles.png)

A lista de atribuições mostrada usa como padrão as **atribuições qualificadas**. Use o menu suspenso para alternar entre atribuições qualificadas e ativas.

Para solicitar a renovação de qualquer uma das atribuições de grupo na lista, selecione a ação **renovar** . Em seguida, forneça um motivo para a solicitação. É útil fornecer uma duração além de qualquer contexto adicional ou uma justificativa de negócios que possa ajudar o administrador de recursos a decidir aprovar ou negar.

![Painel de atribuição de grupo renovar mostrando a caixa de motivo](media/groups-renew-extend/groups-renew-request-form.png)

Depois que a solicitação tiver sido enviada, os administradores de recursos serão notificados sobre uma solicitação pendente para renovar uma atribuição de grupo.

### <a name="admin-approves"></a>Aprovação pelo administrador

Os administradores de recursos podem acessar a solicitação de renovação por meio do link na notificação por email ou acessando Privileged Identity Management na portal do Azure e selecionando **aprovar solicitações** no painel esquerdo.

Quando um administrador seleciona **aprovar** ou **negar**, os detalhes da solicitação são mostrados junto com um campo para fornecer uma justificativa de negócios para os logs de auditoria.

Ao aprovar uma solicitação para renovar uma atribuição de grupo, os administradores de recursos devem inserir uma nova data de início, data de término e tipo de atribuição.

## <a name="next-steps"></a>Próximas etapas

- [Aprovar ou negar solicitações de atribuições de grupo de acesso privilegiado no Privileged Identity Management](groups-approval-workflow.md)
- [Definir configurações de grupo de acesso privilegiado no Privileged Identity Management](groups-role-settings.md)

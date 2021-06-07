---
title: Renovar atribuições de função de recurso do Azure no PIM – Azure AD | Microsoft Docs
description: Saiba como estender ou renovar atribuições de função de recurso do Azure no PIM (Azure AD Privileged Identity Management).
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f0d22fc540aae448a3da731b709a3b4ea13a69d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84742226"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Estender ou renovar atribuições de função de recurso do Azure no Privileged Identity Management

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) fornece controles para gerenciar o acesso e o ciclo de vida de atribuição para recursos do Azure. Os administradores podem atribuir funções usando as propriedades de data e hora de início e término. Quando a extremidade de atribuição se aproxima, Privileged Identity Management envia notificações por email aos usuários ou grupos afetados. Ele também envia notificações por e-mail aos administradores do recurso para garantir que o acesso apropriado seja mantido. As atribuições podem ser renovadas e permanecer visíveis em um estado expirado por até 30 dias, mesmo que o acesso não seja estendido.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar?

Somente os administradores do recurso podem estender ou renovar atribuições de função. O usuário ou grupo afetado pode solicitar a extensão das funções que estão prestes a expirar e a solicitação para renovar as funções que já expiraram.

## <a name="when-are-notifications-sent"></a>Quando as notificações são enviadas?

Privileged Identity Management envia notificações por email para administradores e usuários afetados ou grupos de funções que estão expirando em 14 dias e um dia antes da expiração. Ele envia um e-mail adicional quando uma atribuição expira oficialmente.

Os administradores recebem notificações quando um usuário ou grupo atribuiu solicitações de função expiradas ou expiradas para estender ou renovar. Quando um administrador específico resolve a solicitação, todos os outros administradores são notificados da decisão de resolução (aprovada ou recusada). Em seguida, o usuário ou grupo solicitante é notificado sobre a decisão.

## <a name="extend-role-assignments"></a>Estender atribuições de função

As etapas a seguir descrevem o processo de solicitação, resolução ou administração de uma extensão ou renovação de uma atribuição de função.

### <a name="self-extend-expiring-assignments"></a>Estender automaticamente as atribuições de expiração

Os usuários ou grupos atribuídos a uma função podem estender as atribuições de função de expiração diretamente da guia **qualificada** ou **ativa** na página **minhas funções** de um recurso e na página **minhas funções** de nível superior do portal de Privileged Identity Management. Os usuários ou grupos podem solicitar a extensão de funções qualificadas e ativas (atribuídas) que expiram nos próximos 14 dias.

![Recursos do Azure – página minhas funções listando funções qualificadas com uma coluna de ação](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando a data e a hora de término da atribuição estiverem dentro de 14 dias, o botão para **Estender** se tornará um link ativo na interface do usuário. No exemplo a seguir, suponha que a data atual seja 27 de março.

![Coluna de ação com links para ativar ou estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Para solicitar uma extensão dessa atribuição de função, selecione **Estender** para abrir o formulário de solicitação.

![Estender o painel de atribuição de função com uma caixa de motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Para visualizar informações sobre a atribuição original, expanda **Detalhes da atribuição**. Digite uma razão para a solicitação de extensão e, em seguida, selecione **Estender**.

>[!NOTE]
>Recomendamos incluir os detalhes de por que a extensão é necessária e por quanto tempo a extensão deve ser concedida (se você tiver essa informação).

![Estender painel de atribuição de função com detalhes de atribuição expandidos](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Em questão de minutos, os administradores de recursos recebem uma notificação por email solicitando que eles revisem a solicitação de extensão. Se uma solicitação para estender já tiver sido enviada, uma notificação do Azure aparecerá no Portal.

![Notificação explicando que já existe uma extensão de atribuição de função pendente existente](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Vá para a página **solicitações pendentes** para exibir o status de sua solicitação ou para cancelá-la.

![Recursos do Azure-página de solicitações pendentes listando qualquer solicitação pendente e um link para cancelar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Extensão aprovada pelo administrador

Quando um usuário ou grupo envia uma solicitação para estender uma atribuição de função, os administradores de recursos recebem uma notificação por email que contém os detalhes da atribuição original e o motivo da solicitação. A notificação inclui um link direto com a solicitação para o administrador aprovar ou negar.

Além de usar o link do email a seguir, os administradores podem aprovar ou negar solicitações acessando o portal de administração do Privileged Identity Management e selecionando **aprovar solicitações** no painel esquerdo.

![Recursos do Azure – aprovar solicitações de listagem de páginas e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador seleciona **aprovar** ou **negar**, os detalhes da solicitação são mostrados, juntamente com um campo para fornecer uma justificativa de negócios para os logs de auditoria.

![Aprovar solicitação de atribuição de função com motivo do solicitante, tipo de atribuição, hora de início, hora de término e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar uma solicitação para estender a atribuição de função, os administradores de recursos podem escolher uma nova data de início, uma data de término e um tipo de atribuição. Alterar o tipo de atribuição pode ser necessário se o administrador quiser fornecer acesso limitado para concluir uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de **Qualificado** para **Ativo**. Isso significa que eles podem fornecer acesso ao solicitante sem precisar que eles sejam ativados.

### <a name="admin-initiated-extension"></a>Extensão iniciada pelo administrador

Se um usuário atribuído a uma função não solicitar uma extensão para a atribuição de função, um administrador poderá estender uma atribuição em nome do usuário. As extensões administrativas da atribuição de função não exigem aprovação, mas as notificações são enviadas a todos os outros administradores após a extensão da função.

Para estender uma atribuição de função, navegue até a função de recurso ou exibição de atribuição em Privileged Identity Management. Localize a atribuição que requer uma extensão. Em seguida, selecione **estender** na coluna ação.

![Recursos do Azure – página de atribuições listando funções qualificadas com links para estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renovar atribuições de função

Embora seja conceitualmente semelhante ao processo para solicitar uma extensão, o processo para renovar uma atribuição de função expirada é diferente. Usando as etapas, as atribuições e os administradores a seguir podem renovar o acesso às funções expiradas quando necessário.

### <a name="self-renew"></a>Renovação automática

Os usuários que não podem mais acessar recursos podem acessar até 30 dias de histórico de atribuição expirado. Para fazer isso, navegue até **funções Meus** no painel esquerdo e, em seguida, selecione o **expirado funções** guia na seção de funções de recurso do Azure.

![Página minhas funções-guia funções expiradas](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

A lista de papéis mostrada é padronizada para **Funções elegíveis**. Use o menu suspenso para alternar entre as funções atribuídas Elegíveis e Ativas.

Para solicitar a renovação de qualquer uma das atribuições de função na lista, selecione a ação **Renovar**. Em seguida, forneça um motivo para a solicitação. É útil fornecer uma duração além de qualquer contexto adicional ou uma justificativa de negócios que possa ajudar o administrador de recursos a decidir aprovar ou negar.

![Painel renovar atribuição de função mostrando a caixa de motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Depois que a solicitação é enviada, os administradores de recursos são notificados sobre uma solicitação pendente para renovar uma atribuição de função.

### <a name="admin-approves"></a>Aprovação pelo administrador

Os administradores de recursos podem acessar a solicitação de renovação por meio do link na notificação por email ou acessando Privileged Identity Management na portal do Azure e selecionando **aprovar solicitações** no painel esquerdo.

![Recursos do Azure – aprovar solicitações de listagem de páginas e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador seleciona **aprovar** ou **negar**, os detalhes da solicitação são mostrados junto com um campo para fornecer uma justificativa de negócios para os logs de auditoria.

![Aprovar solicitação de atribuição de função com motivo do solicitante, tipo de atribuição, hora de início, hora de término e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar uma solicitação para renovar a atribuição de função, os administradores de recursos devem inserir uma nova data de início, uma data de término e um tipo de atribuição.

### <a name="admin-renew"></a>Renovação pelo administrador

Os administradores de recursos podem renovar atribuições de função expiradas da guia **Members** no menu de navegação à esquerda de um recurso. Eles também podem renovar atribuições de função expiradas de dentro do **expirado** guia funções de uma função de recurso.

Para visualizar uma lista de todas as atribuições de funções expiradas, na tela **Members**, selecione **Expired roles**.

![Recursos do Azure-página Membros listando funções expiradas com links para renovar](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Próximas etapas

- [Aprovar ou negar solicitações para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)

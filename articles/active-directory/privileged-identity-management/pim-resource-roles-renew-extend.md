---
title: Estender ou renovar atribuições de função de recursos do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como estender ou renovar atribuições de função de recurso do Azure no PIM (Azure AD Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 147b1714c88fd93a3098ecf7a28164a227af29de
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476295"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Estender ou renovar atribuições de função de recurso do Azure no PIM

Do Azure Active Directory (Azure AD) Privileged identidade Management (PIM) introduz novos controles para gerenciar o ciclo de vida de atribuição e de acesso para recursos do Azure. Os administradores podem atribuir associação usando propriedades de data e hora de início e término. Quando a atribuição termina, o PIM envia notificações por e-mail aos usuários ou grupos afetados. Ele também envia notificações por e-mail aos administradores do recurso para garantir que o acesso apropriado seja mantido. As atribuições podem ser renovadas e permanecer visíveis em um estado expirado por até 30 dias, mesmo que o acesso não seja estendido.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar?

Somente os administradores do recurso podem estender ou renovar atribuições de função. O membro afetado pode solicitar a extensão de funções que estão prestes a expirar e solicitar a renovação de funções que já expiraram.

## <a name="when-are-notifications-sent"></a>Quando as notificações são enviadas?

O PIM envia notificações por e-mail para administradores e membros afetados de funções que estão expirando dentro de 14 dias e um dia antes da expiração. Ele envia um e-mail adicional quando uma atribuição expira oficialmente. 

Os administradores recebem notificações quando um membro de uma função expirada ou expirando solicita a extensão ou renovação. Quando um administrador específico resolve a solicitação, todos os outros administradores são notificados da decisão de resolução (aprovada ou recusada). Em seguida, o membro solicitante é notificado da decisão. 

## <a name="extend-role-assignments"></a>Estender atribuições de função

As etapas a seguir descrevem o processo de solicitação, resolução ou administração de uma extensão ou renovação de uma atribuição de função. 

### <a name="member-extend"></a>Extensão por membros

Os membros de uma atribuição de função podem estender atribuições de função com prazo de validade diretamente da guia **Elegível** ou **Ativo** na página **Minhas funções** de um recurso e do nível superior**Minhas funções** página do portal PIM. Membros podem solicitar a extensão de funções qualificadas e ativas (atribuídas) que expirarão dentro de 14 dias.

![Recursos do Azure - minhas funções página lista de funções qualificadas com uma coluna de ação](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Quando a data e a hora de término da atribuição estiverem dentro de 14 dias, o botão para **Estender** se tornará um link ativo na interface do usuário. No exemplo a seguir, suponha que a data atual seja 27 de março.

![Coluna de ação com links para ativar ou estendida](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Para solicitar uma extensão dessa atribuição de função, selecione **Estender** para abrir o formulário de solicitação.

![Estender o painel de atribuição de função com uma caixa de motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Para visualizar informações sobre a atribuição original, expanda **Detalhes da atribuição**. Digite uma razão para a solicitação de extensão e, em seguida, selecione **Estender**.

>[!Note]
>Recomendamos incluir os detalhes de por que a extensão é necessária e por quanto tempo a extensão deve ser concedida (se você tiver essa informação).

![Estender o painel de atribuição de função com detalhes da atribuição expandidos](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Em questão de momentos, os administradores de recursos recebem uma notificação por e-mail solicitando que eles revisem a solicitação de extensão. Se uma solicitação para estender já tiver sido enviada, uma notificação de notificação será exibida na parte superior do portal do Azure, explicando o erro.

![Notificação explicando o que há já existente pendente de extensão de atribuição de função](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Vá para o **solicitações pendentes** página no painel esquerdo para exibir o status de sua solicitação ou para cancelá-la.

![Recursos do Azure - pendente da página de solicitações pendentes solicitada e um link para cancelar a listagem](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Aprovação pelo administrador

Quando um membro envia uma solicitação para estender uma atribuição de função, os administradores de recursos recebem uma notificação por email que contém os detalhes da atribuição original e o motivo da solicitação. A notificação inclui um link direto com a solicitação para o administrador aprovar ou negar. 

Além de usar o seguinte link do email, os administradores podem aprovar ou negar solicitações acessando o portal de administração do PIM e selecionando **Approve requests** no painel esquerdo.

![Recursos do Azure - aprovar solicitações de página solicitações de listagem e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um Administrador seleciona **Aprovar** ou **Negar**, os detalhes da solicitação são exibidos, junto com um campo para fornecer justificativa para os logs de auditoria.

![Aprovar solicitação de atribuição de função com o motivo do solicitante, tipo de atribuição, a hora de início, hora de término e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar uma solicitação para estender a atribuição de função, os administradores de recursos podem escolher uma nova data de início, uma data de término e um tipo de atribuição. Alterar o tipo de atribuição pode ser necessário se o administrador quiser fornecer acesso limitado para concluir uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de **Qualificado** para **Ativo**. Isso significa que eles podem fornecer acesso ao solicitante sem precisar que eles sejam ativados.

### <a name="admin-extend"></a>Extensão pelo administrador

Se um membro da função esquecer ou não puder solicitar uma extensão de associação de função, um administrador poderá estender uma atribuição em nome do membro. As extensões administrativas da associação de função não exigem aprovação, mas as notificações são enviadas a todos os outros administradores depois que a função foi estendida.

Para estender uma associação de função, navegue até a função de recurso ou exibição de membro no PIM. Localize o membro que requer uma extensão. Em seguida, selecione **estender** na coluna ação.

![Recursos do Azure - página de membros da lista de funções qualificadas com links para estender](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Renovar atribuições de função

Embora seja conceitualmente semelhante ao processo para solicitar uma extensão, o processo para renovar uma atribuição de função expirada é diferente. Usando as etapas a seguir, membros e administradores podem renovar o acesso a funções expiradas quando necessário.

### <a name="member-renew"></a>Renovação por membro

Membros que não podem mais acessar recursos podem acessar até 30 dias do histórico de atribuições expiradas. Para fazer isso, navegue até **funções Meus** no painel esquerdo e, em seguida, selecione o **expirado funções** guia na seção de funções de recurso do Azure.

![Minha página de funções - guia de funções expiradas](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

A lista de papéis mostrada é padronizada para **Funções elegíveis**. Use o menu suspenso para alternar entre as funções atribuídas Elegíveis e Ativas.

Para solicitar a renovação de qualquer uma das atribuições de função na lista, selecione a ação **Renovar**. Em seguida, forneça um motivo para a solicitação. É útil fornecer uma duração além de qualquer contexto adicional que ajude o administrador de recursos a decidir aprovar ou negar.

![Renovar o painel de atribuição de função, mostrando a caixa de motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Depois que a solicitação é enviada, os administradores de recursos são notificados sobre uma solicitação pendente para renovar uma atribuição de função.

### <a name="admin-approves"></a>Aprovação pelo administrador

Os administradores de recursos podem acessar a solicitação de renovação a partir do link na notificação por e-mail ou acessando o PIM do portal do Azure e selecionando **Aprovar solicitações** no painel esquerdo.

![Recursos do Azure - aprovar solicitações de página solicitações de listagem e links para aprovar ou negar](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Quando um administrador seleciona **Aprovar** ou **Negar**, os detalhes da solicitação são mostrados junto com um campo para fornecer justificativa para os logs de auditoria.

![Aprovar solicitação de atribuição de função com o motivo do solicitante, tipo de atribuição, a hora de início, hora de término e motivo](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Ao aprovar uma solicitação para renovar a atribuição de função, os administradores de recursos devem inserir uma nova data de início, uma data de término e um tipo de atribuição. 

### <a name="admin-renew"></a>Renovação pelo administrador

Os administradores de recursos podem renovar atribuições de função expiradas da guia **Members** no menu de navegação à esquerda de um recurso. Eles também podem renovar atribuições de função expiradas de dentro do **expirado** guia funções de uma função de recurso.

Para visualizar uma lista de todas as atribuições de funções expiradas, na tela **Members**, selecione **Expired roles**.

![Recursos do Azure - página de membros a funções expiradas com links para renovar a listagem](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Próximas etapas

- [Aprovar ou negar as solicitações para funções de recurso do Azure no PIM](pim-resource-roles-approval-workflow.md)
- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)

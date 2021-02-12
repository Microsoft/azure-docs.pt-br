---
title: Definir configurações de grupos de acesso privilegiado em PIM-Azure Active Directory | Microsoft Docs
description: Saiba como configurar as configurações de grupos de funções atribuíveis no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: abb7f93437cd45914d3824e9f557241ba0d71162
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835383"
---
# <a name="configure-privileged-access-group-settings-preview-in-privileged-identity-management"></a>Definir configurações de grupo de acesso privilegiado (versão prévia) no Privileged Identity Management

As configurações de função são as configurações padrão que são aplicadas ao proprietário do grupo e atribuições de acesso privilegiado ao membro do grupo no Privileged Identity Management (PIM). Use as etapas a seguir para configurar o fluxo de trabalho de aprovação para especificar quem pode aprovar ou negar solicitações para elevar o privilégio.

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função de grupo de acesso privilegiado do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário na função de [administrador de função com privilégios](../roles/permissions-reference.md#privileged-role-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **acesso privilegiado (versão prévia)**.

1. Selecione o grupo que você deseja gerenciar.

    ![Grupos de acesso privilegiados filtrados por um nome de grupo](./media/groups-role-settings/group-select.png)

1. Selecione **Configurações**.

    ![Página de configurações Listando configurações de grupo para o grupo selecionado](./media/groups-role-settings/group-settings-select-role.png)

1. Selecione a função de proprietário ou membro cujas configurações você deseja exibir ou alterar. Você pode exibir as configurações atuais da função na página de **detalhes de configuração de função** .

    ![Página de detalhes de configuração de função listando várias configurações de atribuição e ativação](./media/groups-role-settings/group-role-setting-details.png)

1. Selecione **Editar** para abrir a página **Editar configuração de função** . A guia **ativação** permite que você altere as configurações de ativação de função, incluindo se deseja permitir atribuições ativas e qualificadas permanentes.

    ![Página Editar configurações de função com a guia ativação aberta](./media/groups-role-settings/role-settings-activation-tab.png)

1. Selecione a guia **atribuição** para abrir a guia Configurações de atribuição. Essas configurações controlam as configurações de atribuição de Privileged Identity Management para essa função.

    ![Guia atribuição de função na página Configurações de função](./media/groups-role-settings/role-settings-assignment-tab.png)

1. Use a guia **notificação** ou o botão **Avançar: ativação** na parte inferior da página para acessar a guia Configuração de notificação para essa função. Essas configurações controlam todas as notificações por email relacionadas a essa função.

    ![Guia notificações de função na página Configurações de função](./media/groups-role-settings/role-settings-notification-tab.png)

1. Selecione o botão **Atualizar** a qualquer momento para atualizar as configurações de função.

Na guia **notificações** da página Configurações de função, Privileged Identity Management habilita o controle granular sobre quem recebe notificações e quais notificações elas recebem.

- **Desligando um email**<br>Você pode desativar emails específicos desmarcando a caixa de seleção destinatário padrão e excluindo destinatários adicionais.  
- **Limitar emails a endereços de email especificados**<br>Você pode desativar os emails enviados aos destinatários padrão desmarcando a caixa de seleção destinatário padrão. Em seguida, você pode adicionar endereços de email adicionais como destinatários adicionais. Se você quiser adicionar mais de um endereço de email, separe-os usando um ponto-e-vírgula (;).
- **Enviar emails para destinatários padrão e destinatários adicionais**<br>Você pode enviar emails para o destinatário padrão e para o destinatário adicional, marcando a caixa de seleção destinatário padrão e adicionando endereços de email para destinatários adicionais.
- **Somente emails críticos**<br>Para cada tipo de email, você pode marcar a caixa de seleção para receber emails críticos apenas. Isso significa que Privileged Identity Management continuará a enviar emails para os destinatários configurados somente quando o email exigir uma ação imediata. Por exemplo, emails solicitando que os usuários estendam suas atribuições de função não serão disparados enquanto um email que exigir que os administradores aprovem uma solicitação de extensão será disparado.

## <a name="assignment-duration"></a>Duração dae atribuição

É possível escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificada e ativa) ao definir as configurações de uma função. Essas opções se tornam a duração máxima padrão quando um usuário é atribuído à função no Privileged Identity Management.

Você pode escolher uma destas opções de duração de atribuição **qualificadas** :

| | |
| --- | --- |
| **Permitir atribuição qualificada permanente** | Os administradores de recursos podem atribuir uma atribuição qualificada permanente. |
| **Expirar atribuição qualificada após** | Os administradores de recursos podem exigir que todas as atribuições qualificadas tenham uma data de início e de término especificadas. |

E, você pode escolher uma destas opções de duração da atribuição **ativa**:

| | |
| --- | --- |
| **Permitir atribuição ativa permanente** | Os administradores de recursos podem atribuir uma atribuição ativa permanente. |
| **Expirar atribuição ativa após** | Os administradores de recursos podem exigir que todas as atribuições ativas tenham uma data de início e de término especificadas. |

> [!NOTE]
> Todas as atribuições que têm uma data de término especificada poderão ser renovadas por administradores de recursos. Além disso, os usuários podem iniciar solicitações de autoatendimento para [estender ou renovar atribuições de função](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

Privileged Identity Management fornece imposição opcional da autenticação multifator do Azure AD para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir Autenticação Multifator na atribuição ativa

Em alguns casos, talvez você queira atribuir um usuário ou grupo a uma função por uma duração curta (um dia, por exemplo). Nesse caso, os usuários atribuídos não precisam solicitar ativação. Nesse cenário, Privileged Identity Management não pode impor a autenticação multifator quando o usuário usa sua atribuição de função porque eles já estão ativos na função a partir do momento em que são atribuídos.

Para garantir que o administrador de recursos que está atendendo à atribuição seja quem eles dizem que eles são, você pode impor a autenticação multifator na atribuição ativa marcando a caixa de **atribuição exigir autenticação multifator no Active** .

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir a Autenticação Multifator na ativação

Você pode exigir que os usuários qualificados para uma função comprovem quem estão usando a autenticação multifator do Azure AD antes que possam ser ativados. A autenticação multifator garante que o usuário seja quem dizem que eles estão com certeza razoável. A aplicação dessa opção protege recursos críticos em situações em que a conta do usuário pode ter sido comprometida.

Para exigir a autenticação multifator antes da ativação, marque a caixa de seleção **exigir autenticação multifator no modo de ativação** .

Para saber mais, confira [Autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duração máxima de ativação

Use o controle deslizante **Duração máxima da ativação** para definir o tempo máximo, em horas, que uma função permanecerá ativa antes de expirar. Esse valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Exigir justificativa

Você pode exigir que os usuários insiram uma justificativa de negócios ao serem ativados. Para exigir justificativa, marque a caixa **Exigir justificativa na atribuição ativa** ou a caixa **Exigir justificativa na ativação**.

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se você quiser exigir aprovação para ativar uma função, siga estas etapas.

1. Marque a caixa de seleção **Exige aprovação para ativar**.

1. Selecione **selecionar aprovadores** para abrir a página **selecionar um membro ou grupo** .

    ![Selecione um painel de usuário ou grupo para selecionar aprovadores](./media/groups-role-settings/group-settings-select-approvers.png)

1. Selecione pelo menos um usuário ou grupo e clique em **selecionar**. Você pode adicionar qualquer combinação de usuários e grupos. É necessário selecionar pelo menos um aprovador. Não há nenhum aprovador padrão.

    Suas seleções serão exibidas na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, selecione **Atualizar** para salvar suas alterações.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir Associação de grupo de acesso privilegiado ou propriedade no PIM](groups-assign-member-owner.md)

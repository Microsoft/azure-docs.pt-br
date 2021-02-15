---
title: Definir configurações de função de recurso do Azure no PIM – Azure AD | Microsoft Docs
description: Saiba como definir configurações de função de recurso do Azure no Azure AD PIM (Privileged Identity Management).
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
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ded81389fae5954c0e488f634945b4f1c1fe4c8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009034"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Definir configurações de função de recurso do Azure no Privileged Identity Management

Ao definir as configurações de função de recurso do Azure, você define as configurações padrão que são aplicadas às atribuições de função de recurso do Azure no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Use os procedimentos a seguir para configurar o fluxo de trabalho de aprovação e especifique quem pode aprovar ou negar solicitações.

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função de recursos do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário na função de [administrador de função com privilégios](../roles/permissions-reference.md#privileged-role-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **recursos do Azure**.

1. Selecione o recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

    ![Página de recursos do Azure listando recursos que podem ser gerenciados](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Selecione **configurações de função**.

    ![Página de configurações de função listando funções de recurso do Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Selecione a função cujas configurações você deseja configurar.

    ![Página de detalhes de configuração de função listando várias configurações de atribuição e ativação](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Selecione **Editar** para abrir o painel **configurações de função** . A primeira guia permite que você atualize a configuração de ativação de função no Privileged Identity Management.

    ![Página Editar configurações de função com a guia ativação aberta](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Selecione a guia **atribuição** ou o botão **próximo: atribuição** na parte inferior da página para abrir a guia Configuração de atribuição. Essas configurações controlam as atribuições de função feitas dentro da interface Privileged Identity Management.

    ![Guia atribuição de função na página Configurações de função](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Use a guia **notificação** ou o botão **Avançar: ativação** na parte inferior da página para acessar a guia Configuração de notificação para essa função. Essas configurações controlam todas as notificações por email relacionadas a essa função.

    ![Guia notificações de função na página Configurações de função](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    Na guia **notificações** da página Configurações de função, Privileged Identity Management habilita o controle granular sobre quem recebe notificações e quais notificações elas recebem.

    - **Desligando um email**<br>Você pode desativar emails específicos desmarcando a caixa de seleção destinatário padrão e excluindo destinatários adicionais.  

    - **Limitar emails a endereços de email especificados**<br>Você pode desativar os emails enviados aos destinatários padrão desmarcando a caixa de seleção destinatário padrão. Em seguida, você pode adicionar endereços de email adicionais como destinatários adicionais. Se você quiser adicionar mais de um endereço de email, separe-os usando um ponto-e-vírgula (;).

    - **Enviar emails para destinatários padrão e destinatários adicionais**<br>Você pode enviar emails para o destinatário padrão e para o destinatário adicional, marcando a caixa de seleção destinatário padrão e adicionando endereços de email para destinatários adicionais.

    - **Somente emails críticos**<br>Para cada tipo de email, você pode marcar a caixa de seleção para receber emails críticos apenas. Isso significa que Privileged Identity Management continuará a enviar emails para os destinatários configurados somente quando o email exigir uma ação imediata. Por exemplo, emails solicitando que os usuários estendam suas atribuições de função não serão disparados enquanto um email que exigir que os administradores aprovem uma solicitação de extensão será disparado.

1. Selecione o botão **Atualizar** a qualquer momento para atualizar as configurações de função.

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

    ![Selecione um painel de usuário ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um usuário ou grupo e clique em **selecionar**. Você pode adicionar qualquer combinação de usuários e grupos. É necessário selecionar pelo menos um aprovador. Não há nenhum aprovador padrão.

    Suas seleções serão exibidas na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, selecione **Atualizar** para salvar suas alterações.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Configurar alertas de segurança para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-alerts.md)

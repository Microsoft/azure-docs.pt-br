---
title: Definir as configurações de função do Azure AD no PIM – Azure AD | Microsoft Docs
description: Saiba como definir as configurações de função do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 676fdf264cb930069425a330c3da9cd753bf8e0a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567026"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Definir as configurações de função do Azure AD no Privileged Identity Management

Um administrador de função com privilégios pode personalizar Privileged Identity Management (PIM) em sua organização do Azure Active Directory (Azure AD), incluindo a alteração da experiência de um usuário que está ativando uma atribuição de função qualificada.

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isso cria recursos adicionais, bem como [as alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que esteja na função de [administrador de função com privilégios](../roles/permissions-reference.md#privileged-role-administrator) .
1. Abra **Azure ad Privileged Identity Management**. Se você tiver uma faixa na parte superior da página Visão geral, siga as instruções na **nova guia versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior** .

  [![Selecione Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga as etapas neste artigo para aprovar ou negar solicitações para funções do Azure AD.

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário na função de [administrador de função com privilégios](../roles/permissions-reference.md#privileged-role-administrator) .

1. Abra **Azure ad Privileged Identity Management** &gt; configurações de função de **funções do Azure ad** &gt; .

    ![Página de configurações de função listando funções do Azure AD](./media/pim-how-to-change-default-settings/role-settings.png)

1. Selecione a função cujas configurações você deseja configurar.

    ![Página de detalhes de configuração de função listando várias configurações de atribuição e ativação](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Selecione **Editar** para abrir a página Configurações da função.

    ![Página Editar configurações de função com opções para atualizar a atribuição e as configurações de ativação](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    No painel Configurações de função de cada função, há várias configurações que podem ser configuradas.

## <a name="assignment-duration"></a>Duração dae atribuição

É possível escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificada e ativa) ao definir as configurações de uma função. Essas opções se tornam a duração máxima padrão quando um usuário é atribuído à função no Privileged Identity Management.

Você pode escolher uma destas opções de duração de atribuição **qualificadas** :

| | Description |
| --- | --- |
| **Permitir atribuição qualificada permanente** | Administradores globais e administradores de função com privilégios podem atribuir atribuição qualificada permanente. |
| **Expirar atribuição qualificada após** | Administradores globais e administradores de função com privilégios podem exigir que todas as atribuições qualificadas tenham uma data de início e de término especificada. |

E, você pode escolher uma destas opções de duração da atribuição **ativa**:

| | Description |
| --- | --- |
| **Permitir atribuição ativa permanente** | Administradores globais e administradores de função com privilégios podem atribuir uma atribuição ativa permanente. |
| **Expirar atribuição ativa após** | Administradores globais e administradores de função com privilégios podem exigir que todas as atribuições ativas tenham uma data de início e de término especificada. |

> [!NOTE]
> Todas as atribuições que têm uma data de término especificada podem ser renovadas por administradores globais e administradores de função com privilégios. Além disso, os usuários podem iniciar solicitações de autoatendimento para [estender ou renovar atribuições de função](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Exigir autenticação multifator

Privileged Identity Management fornece imposição opcional da autenticação multifator do Azure AD para dois cenários distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Exigir Autenticação Multifator na atribuição ativa

Em alguns casos, talvez você queira atribuir um usuário a uma função por uma duração curta (um dia, por exemplo). Nesse caso, os usuários atribuídos não precisam solicitar ativação. Nesse cenário, Privileged Identity Management não pode impor a autenticação multifator quando o usuário usa sua atribuição de função porque eles já estão ativos na função a partir do momento em que são atribuídos.

Para garantir que o administrador que está atendendo à atribuição seja quem dizem que eles são, você pode impor a autenticação multifator na atribuição ativa marcando a caixa de **atribuição exigir autenticação multifator no Active** .

### <a name="require-multi-factor-authentication-on-activation"></a>Exigir a Autenticação Multifator na ativação

Você pode exigir que os usuários qualificados para uma função comprovem quem estão usando a autenticação multifator do Azure AD antes que possam ser ativados. A autenticação multifator garante que o usuário seja quem dizem que eles estão com certeza razoável. A aplicação dessa opção protege recursos críticos em situações em que a conta do usuário pode ter sido comprometida.

Para exigir a autenticação multifator antes da ativação, marque a caixa **exigir autenticação multifator no modo de ativação** na guia atribuição da **configuração Editar função**.

Para saber mais, confira [Autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duração máxima de ativação

Use o controle deslizante **Duração máxima da ativação** para definir o tempo máximo, em horas, que uma função permanecerá ativa antes de expirar. Esse valor pode ser de uma a 24 horas.

## <a name="require-justification"></a>Exigir justificativa

Você pode exigir que os usuários insiram uma justificativa de negócios ao serem ativados. Para exigir justificativa, marque a caixa **Exigir justificativa na atribuição ativa** ou a caixa **Exigir justificativa na ativação**.

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se definir vários Aprovadores, a aprovação será concluída assim que um deles for aprovado ou negado. Você não pode exigir aprovação de pelo menos dois usuários. Para exigir aprovação para ativar uma função, siga estas etapas.

1. Marque a caixa de seleção **Exige aprovação para ativar**.

1. Selecione **selecionar aprovadores**.

    ![Selecione um painel de usuário ou grupo para selecionar aprovadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Selecione pelo menos um usuário e clique em **selecionar**. É necessário selecionar pelo menos um aprovador. Não há nenhum aprovador padrão.

    Suas seleções serão exibidas na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, selecione **Atualizar** para salvar suas alterações.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="open-role-settings-previous-version"></a>Abrir configurações de função (versão anterior)

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **Configurações**.

    ![Funções do Azure AD – configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Selecione **funções**.

1. Selecione a função cujas configurações você deseja configurar.

    ![Funções do Azure AD – funções de configurações](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na página de configurações de cada função, há diversas configurações que você pode realizar. Essas configurações afetam apenas os usuários que são atribuições **elegíveis**, mas não atribuições **permanentes**.

## <a name="activations"></a>Ativações

Use o controle deslizante **Ativações** para definir o tempo máximo, em horas, que uma função permanecerá ativa antes de expirar. Isso pode ser entre 1 e 72 horas.

## <a name="notifications"></a>Notificações

Use as **notificações** para especificar se os administradores receberão notificações por email quando as funções estiverem ativadas. Essa notificação pode ser útil para detectar ativações não autorizadas ou ilegítimos.

Quando definido como **Habilitar**, as notificações são enviadas para:

- Administrador de função com privilégios
- Administrador de segurança
- Administrador global

Para obter mais informações, consulte [notificações por email em Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Tíquete de Incidente/Solicitação

Use a opção de **tíquete de incidente/solicitação** para exigir que os administradores qualificados incluam um número de tíquete quando ativarem sua função. Essa prática pode tornar as auditorias de acesso de função mais eficientes.

## <a name="multi-factor-authentication"></a>Autenticação Multifator

Use a opção **Autenticação Multifator** para especificar se os usuários deverão confirmar a identidade com MFA antes de poderem ativar as funções. Eles só precisam verificar sua identidade uma vez por sessão, nem sempre que ativam uma função. Há duas dicas para ter em mente quando você habilita a MFA:

- Os usuários que têm contas da Microsoft para seus endereços de email (normalmente @outlook.com , mas nem sempre) não podem se registrar para a autenticação multifator do Azure AD. Se você quiser atribuir funções a usuários com contas da Microsoft, você deve torná-los administradores permanentes ou desabilitar a autenticação multifator para essa função.
- Não é possível desabilitar a autenticação multifator do Azure AD para funções altamente privilegiadas para o Azure AD e o Microsoft 365. Esse recurso de segurança ajuda a proteger as seguintes funções:  
  
  - Administrador da Proteção de Informações do Azure
  - Administrador de cobrança
  - Administrador de aplicativos de nuvem
  - Administrador de conformidade
  - Administrador de acesso condicional
  - Administrador do Dynamics 365
  - Aprovador de acesso do Sistema de Proteção de Dados do Cliente
  - Gravadores de diretório
  - Administradores do Exchange
  - Administrador global
  - Administrador do Intune
  - Administrador do Power BI
  - Administrador de função com privilégios
  - Administrador de segurança
  - Administrador do SharePoint
  - Administrador do Skype for Business
  - Administrador de usuários

Para saber mais, confira [Autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Exigir aprovação

Se você quiser delegar a aprovação necessária para ativar uma função, siga estas etapas.

1. Defina a opção **Requer aprovação** para **Habilitado**. O painel expande com opções para selecionar aprovadores.

    ![Captura de tela que mostra a opção "exigir aprovação" com "habilitar" selecionado.](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Se você não especificar nenhum aprovador, o administrador da função com privilégios se tornará o aprovador padrão e, em seguida, será necessário para aprovar todas as solicitações de ativação para essa função.

1. Para adicionar Aprovadores, clique em **selecionar aprovadores**.

    ![Funções do Azure AD – configurações-exigir aprovação](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Selecione um ou mais Aprovadores, além do administrador de função com privilégios e, em seguida, clique em **selecionar**. É recomendável que você adicione pelo menos dois aprovadores. Mesmo que você se adicione como um aprovador, não é possível aprovar automaticamente uma ativação de função. Suas seleções serão exibidas na lista de aprovadores selecionados.

1. Depois de especificar todas as suas configurações de função, selecione **salvar** para salvar as alterações.

---

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Configurar alertas de segurança para funções do Azure AD no Privileged Identity Management](pim-how-to-configure-security-alerts.md)

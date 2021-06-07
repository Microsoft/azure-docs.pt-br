---
title: Configurar o PIM (função personalizada Privileged Identity Management do Azure AD)
description: Como configurar funções personalizadas do Azure AD no PIM (Privileged Identity Management)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23e60539c704dac457ab6e8706ec0cfe350ed9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835315"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Configurar funções personalizadas do Azure AD no Privileged Identity Management

Um administrador de funções com privilégios pode alterar as configurações de função que se aplicam a um usuário quando eles ativam sua atribuição a uma função personalizada e a outros administradores de aplicativo que estão atribuindo funções personalizadas.

> [!NOTE]
> As funções personalizadas do Azure AD não são integradas às funções de diretório internas durante a versão prévia. Depois que a funcionalidade estiver em disponibilidade geral, o gerenciamento de função ocorrerá na experiência de funções internas. Se você vir a seguinte faixa, essas funções deverão ser gerenciadas [na experiência de funções internas](pim-how-to-activate-role.md) e este artigo não se aplicará:
>
> [![Selecione o Azure AD > Privileged Identity Management](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Abrir configurações de função

Siga estas etapas para abrir as configurações de uma função do Azure AD.

1. Entre no [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal do Azure com uma conta de usuário atribuída à função de administrador de funções com privilégios.
1. Selecione **funções personalizadas do Azure AD (versão prévia)**.

    ![Selecione a versão prévia das funções personalizadas do Azure AD para ver as atribuições de função elegíveis](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Selecione **Configuração** para abrir a página **Configurações**. Selecione a função para as configurações que você deseja definir.
1. Selecione **Editar** para abrir a página **Configurações da função**.

    ![Captura de tela que mostra a página "detalhes de configuração de função" com a ação "Editar" selecionada.](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Configurações de função

Há várias configurações que você pode definir.

### <a name="assignment-duration"></a>Duração dae atribuição

É possível escolher entre duas opções de duração de atribuição para cada tipo de atribuição (qualificada ou ativa) ao definir as configurações de uma função. Essas opções passam a ter a duração máxima padrão quando um membro é atribuído à função no Privileged Identity Management.

É possível escolher uma destas opções de duração de atribuição *qualificada*.

- **Permitir atribuição qualificada permanente**: os administradores podem atribuir uma associação qualificada permanente.
- **Expirar atribuição qualificada após**: os administradores podem exigir que todas as atribuições qualificadas tenham uma data de início e de término especificada.

Além disso, você pode escolher uma destas opções de duração da atribuição *ativa*:

- **Permitir atribuição ativa permanente**: os administradores podem atribuir uma associação ativa permanente.
- **Expirar atribuição ativa após**: os administradores podem exigir que todas as atribuições ativas tenham uma data de início e de término especificada.

### <a name="require-azure-ad-multi-factor-authentication"></a>Exigir a Autenticação Multifator do Azure AD

Privileged Identity Management fornece imposição opcional da autenticação multifator do Azure AD para dois cenários distintos.

- **Exigir Autenticação Multifator na atribuição ativa**

  Se você quiser atribuir um membro a uma função por uma curta duração (por exemplo, um dia), poderá demorar muito para exigir que os membros atribuídos solicitem a ativação. Nesse cenário, o Privileged Identity Management não pode impor a autenticação multifator quando o usuário ativa sua atribuição de função, pois já está ativo na função desde o momento em que ele é atribuído. Para verificar se o administrador que atende à atribuição é quem ele diz que é, marque a caixa **Exigir Autenticação Multifator na atribuição ativa**.

- **Exigir a Autenticação Multifator na ativação**

  Você pode exigir que usuários qualificados sejam atribuídos a uma função para se registrarem na autenticação multifator do Azure AD antes que eles possam ser ativados. Esse processo garante que o usuário solicitando ativação seja quem diz ser com uma certeza razoável. A imposição dessa opção protege funções críticas em situações em que a conta do usuário pode ter sido comprometida. Para exigir um membro qualificado para executar a autenticação multifator do Azure AD antes da ativação, selecione a caixa **exigir autenticação multifator no modo de ativação** .

Para saber mais, confira [Autenticação multifator e Privileged Identity Management](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Duração máxima de ativação

Use o controle deslizante **Duração máxima da ativação** para definir o tempo máximo, em horas, que uma função permanecerá ativa antes de expirar. Esse valor pode ser de 1 a 24 horas.

### <a name="require-justification"></a>Exigir justificativa

É possível exigir que os membros insiram uma justificativa na atribuição ativa ou quando são ativados. Para exigir justificativa, marque a caixa de seleção **Exigir justificativa na atribuição ativa** ou a caixa **Exigir justificativa na ativação**.

### <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Se você quiser exigir aprovação para ativar uma função, siga estas etapas.

1. Marque a caixa de seleção **Exigir aprovação para ativar**.
1. Selecione **Selecionar aprovadores** para abrir a lista **Selecionar um membro ou grupo**.

    ![Abra a função personalizada do Azure AD para editar as configurações](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Selecione pelo menos um membro ou grupo e clique em **Selecionar**. É necessário selecionar pelo menos um aprovador. Não há nenhum aprovador padrão. Suas seleções serão exibidas na lista de aprovadores selecionados.
1. Após especificar as configurações de função, selecione **Atualizar** para salvar suas alterações.

## <a name="next-steps"></a>Próximas etapas

- [Ativar uma função personalizada do Azure AD](azure-ad-custom-roles-activate.md)
- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Definições de função no Azure AD](../roles/permissions-reference.md)

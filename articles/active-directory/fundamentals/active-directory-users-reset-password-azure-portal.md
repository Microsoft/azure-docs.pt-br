---
title: Redefinir a senha de um usuário - Azure Active Directory | Microsoft Docs
description: Instruções sobre como redefinir a senha de um usuário usando o Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8809f8c168e7095f05587c7a572e08287637dc5a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034584"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Redefinir a senha de um usuário usando o Azure Active Directory

Como um administrador, você pode redefinir a senha de um usuário se a senha for esquecida, se o usuário for bloqueado em um dispositivo ou se o usuário nunca recebeu uma senha.

>[!Note]
>A menos que o locatário do Azure AD seja o diretório base de um usuário, você não poderá redefinir a senha. Isso significa que, se o usuário estiver conectado na organização usando uma conta de outra organização, uma conta Microsoft ou uma conta do Google, você não poderá redefinir a senha.<br><br>Se o usuário tiver uma fonte de autoridade, como Windows Server Active Directory, somente será possível redefinir a senha se você ativar o write-back de senha.<br><br>Se o usuário tiver uma fonte de autoridade como Azure AD Externo, não será possível redefinir a senha. Somente o usuário ou um administrador no Azure AD externo pode redefinir a senha.

>[!Note]
>Se você não for um administrador e estiver procurando para obter instruções sobre como redefinir sua própria senha corporativa ou de estudante, consulte [Redefinir a senha corporativa ou de estudante](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Para redefinir uma senha

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de usuário ou administrador de senha. Para obter mais informações sobre as funções disponíveis, consulte [funções internas do Azure ad](../roles/permissions-reference.md)

2. Selecione **Azure Active Directory**, selecione **Usuários**, pesquise e selecione o usuário que precisa da redefinição e, em seguida, selecione **Redefinir Senha**.

    A página **Alain Charon - Perfil** aparece com a opção **Redefinir senha**.

    ![Página do perfil do usuário, com a opção Redefinir senha destacada](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Na página **Redefinir senha**, selecione **Redefinir senha**.

    > [!Note]
    > Ao usar Azure Active Directory, uma senha temporária é gerada automaticamente para o usuário. Ao usar Active Directory local, você cria a senha para o usuário.

4. Copie a senha e entregue ao usuário. O usuário será solicitado a alterar a senha durante o próximo processo de entrada.

    >[!Note]
    >A senha temporária nunca expira. Na próxima vez em que o usuário entrar, a senha continuará funcionando, independentemente de quanto tempo se passou desde que a senha temporária foi gerada.

> [!IMPORTANT]
> Se um administrador não puder redefinir a senha do usuário e nos logs de eventos do aplicativo no servidor de Azure AD Connect o seguinte código de erro HR = 80231367 será visto, examine os atributos do usuário em Active Directory.  Se o atributo **AdminCount** for definido como 1, isso impedirá que um administrador redefina a senha do usuário.  O atributo **AdminCount** deve ser definido como 0, para que os administradores redefinam a senha do usuário.


## <a name="next-steps"></a>Próximas etapas

Após redefinir a senha do usuário, você poderá executar os processos básicos a seguir:

- [Adicionar ou excluir usuários](add-users-azure-active-directory.md)

- [Atribuir funções aos usuários](active-directory-users-assign-role-azure-portal.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

Ou poderá executar cenários de usuário mais complexos como atribuir delegados, usar políticas e compartilhar contas de usuários. Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gerenciamento de usuário do Azure Active Directory](../enterprise-users/index.yml).

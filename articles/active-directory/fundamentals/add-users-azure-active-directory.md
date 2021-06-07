---
title: Adicionar ou excluir usuários – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar novos usuários ou excluir usuários existentes usando o Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36455b3f6395216bd1ed26c8bd193f0e3ca10b5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594590"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Adicionar ou excluir usuários usando o Azure Active Directory

Adicione novos usuários ou exclua usuários existentes da organização do Azure Active Directory (Azure AD). Para adicionar ou excluir usuários, você deve ser um administrador de usuário ou um administrador global.

## <a name="add-a-new-user"></a>Adicionar um novo usuário

Você pode criar um novo usuário usando o portal do Azure Active Directory.

Para adicionar um novo usuário, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de usuário para a organização.

1. Procure e selecione *Azure Active Directory* em qualquer página.

1. Selecione **usuários** e, em seguida, selecione **novo usuário**.

    ![Adicionar um usuário por meio de usuários-todos os usuários no Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Na página do **usuário** , insira as informações para este usuário:

   - **Nome**. Obrigatórios. O primeiro e último nome do novo usuário. Por exemplo, *Mary Parker*.

   - **Nome de usuário**. Obrigatórios. O nome de usuário do novo usuário. Por exemplo, `mary@contoso.com`.

     A parte do domínio do nome de usuário deve usar o nome de domínio padrão inicial, *\<yourdomainname> . onmicrosoft.com* ou um nome de domínio personalizado, como *contoso.com*. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [Adicionar seu nome de domínio personalizado usando o portal de Azure Active Directory](add-custom-domain.md).

   - **Grupos**. Opcionalmente, você pode adicionar o usuário a um ou mais grupos existentes. Você também pode adicionar o usuário aos grupos posteriormente. Para obter mais informações sobre como adicionar usuários a grupos, consulte [criar um grupo básico e adicionar membros usando Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Função do diretório**: se precisar de permissões administrativas do Azure AD para o usuário, você poderá adicioná-las a uma função do Azure AD. Você pode atribuir o usuário para ser um administrador global ou uma ou mais das funções de administrador limitadas no Azure AD. Para mais informações sobre como atribuir funções, consulte [Como atribuir funções aos usuários](active-directory-users-assign-role-azure-portal.md).

   - **Informações do trabalho**: você pode adicionar mais informações sobre o usuário aqui ou fazer isso mais tarde. Para obter mais informações sobre como adicionar informações do usuário, consulte [Como adicionar ou alterar as informações do perfil do usuário](active-directory-users-profile-azure-portal.md).

1. Copie a senha gerada automaticamente fornecida na caixa **senha** . Você precisará fornecer essa senha ao usuário para entrar pela primeira vez.

1. Selecione **Criar**.

O usuário é criado e adicionado à sua organização do Azure AD.

## <a name="add-a-new-guest-user"></a>Adicionar um novo usuário convidado

Você também pode convidar o novo usuário convidado a colaborar com sua organização selecionando **convidar usuário** na página **novo usuário** . Se as configurações de colaboração externa da sua organização estiverem configuradas de forma que você tenha permissão para convidar convidados, o usuário receberá um convite por email para que ele possa começar a colaborar. Para obter mais informações sobre como convidar usuários de colaboração B2B, consulte [convidar usuários B2B para Azure Active Directory](../external-identities/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Adicionar um usuário do consumidor

Pode haver cenários nos quais você deseja criar manualmente contas de consumidor em seu diretório Azure Active Directory B2C (Azure AD B2C). Para obter mais informações sobre como criar contas de consumidor, consulte [criar e excluir usuários do consumidor no Azure ad B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Adicione um novo usuário em um ambiente híbrido

Se você tiver um ambiente com o Azure Active Directory (nuvem) e o Windows Server Active Directory (local), poderá adicionar novos usuários sincronizando os dados da conta de usuário existente. Para obter mais informações sobre ambientes e usuários híbridos, consulte [Integre seus diretórios locais ao Active Directory do Azure](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Excluir um usuário

Você pode excluir um usuário existente usando o portal do Azure Active Directory.

>[!Note]
>Você deve ter uma atribuição de função de administrador global ou de usuário para excluir usuários em sua organização. Os administradores globais podem excluir qualquer usuário, incluindo outros administradores. Os administradores de usuários podem excluir qualquer usuário não administrador, administradores de assistência técnica e outros administradores de usuários. Para obter mais informações, consulte [permissões de função de administrador no Azure ad](../roles/permissions-reference.md).

Para excluir um usuário, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de usuário para a organização.

1. Procure e selecione *Azure Active Directory* em qualquer página.

1. Pesquise e selecione o usuário que você deseja excluir do seu locatário do Azure AD. Por exemplo, _Mary Parker_.

1. Selecione **Excluir usuário**.

    ![Usuários - página Todos os usuários com Excluir usuário destacado](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

O usuário é excluído e não aparece mais na página **Usuários - Todos os usuários**. O usuário pode ser visto na página **Usuários excluídos** pelos próximos 30 dias e pode ser restaurado durante esse período. Para obter mais informações sobre como restaurar um usuário, consulte [restaurar ou remover um usuário excluído recentemente usando Azure Active Directory](active-directory-users-restore.md).

Quando um usuário é excluído, todas as licenças consumidas pelo usuário são disponibilizadas para outros usuários.

>[!Note]
>Para atualizar a identidade, as informações de contato ou as informações de trabalho para os usuários cuja fonte de autoridade é o Windows Server Active Directory, você deve usar o Windows Server Active Directory. Depois de concluir a atualização, você deve aguardar a conclusão do próximo ciclo de sincronização antes de ver as alterações.

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar os usuários, você pode fazer os seguintes processos básicos:

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Atribuir funções aos usuários](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Trabalhar com usuários e grupos dinâmicos](../enterprise-users/groups-create-rule.md)

Ou você pode fazer outras tarefas de gerenciamento de usuário, como [Adicionar usuários convidados de outro diretório](../external-identities/what-is-b2b.md) ou [restaurar um usuário excluído](active-directory-users-restore.md). Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gerenciamento de usuário do Azure Active Directory](../enterprise-users/index.yml).
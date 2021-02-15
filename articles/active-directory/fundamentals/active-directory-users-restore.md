---
title: Restaurar ou remover permanentemente o usuário excluído recentemente-Azure AD
description: Como exibir usuários restauráveis, restaurar um usuário excluído ou excluir permanentemente um usuário com o Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514382"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Restaurar ou remover permanentemente um usuário excluído recentemente usando o Azure Active Directory
Depois que você excluir um usuário, a conta permanecerá em um estado suspenso por 30 dias. Durante essa janela de 30 dias, a conta do usuário pode ser restaurada, juntamente com todas as suas propriedades. Depois que a janela de 30 dias passa, o processo de exclusão permanente é iniciado automaticamente.

Você pode visualizar seus usuários restauráveis, restaurar um usuário excluído ou excluir permanentemente um usuário usando o Azure AD (Azure Active Directory) no portal do Azure.

>[!Important]
>Nem você nem o suporte ao cliente da Microsoft podem restaurar um usuário excluído permanentemente.

## <a name="required-permissions"></a>Permissões necessárias
Você deve ter uma das seguintes funções para restaurar e excluir permanentemente usuários.

- Administrador global

- Suporte de camada 1 do parceiro

- Suporte de camada 2 do parceiro

- Administrador de usuários

## <a name="view-your-restorable-users"></a>Visualize seus usuários restauráveis
Você pode ver todos os usuários que foram excluídos há menos de 30 dias. Esses usuários podem ser restaurados.

### <a name="to-view-your-restorable-users"></a>Para exibir usuários restauráveis
1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador global para a organização.

2. Selecione **Azure Active Directory**, selecione **usuários** e, em seguida, selecione **usuários excluídos**.

    Examine a lista de usuários que estão disponíveis para restauração.

    ![Usuários - página de usuários excluídos, com usuários que ainda podem ser restaurados](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Restaurar um usuário recentemente excluído

Quando uma conta de usuário é excluída da organização, a conta fica em um estado suspenso e todas as informações relacionadas à organização são preservadas. Quando você restaura um usuário, essas informações da organização também são restauradas.

> [!Note]
> Depois que um usuário é restaurado, as licenças que foram atribuídas ao usuário no momento da exclusão também são restauradas mesmo que não haja estações disponíveis para essas licenças. Se você estiver consumindo mais licenças mais do que você comprou, sua organização poderá estar temporariamente fora de conformidade para uso da licença.

### <a name="to-restore-a-user"></a>Para restaurar um usuário
1. Na página **Usuários - Usuários excluídos**, pesquise e selecione um dos usuários disponíveis. Por exemplo, _Mary Parker_.

2. Selecione **Restaurar usuário**.

    ![Usuários - página de usuários excluídos, com a opção de restauração de usuário realçada](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Excluir permanentemente um usuário
Você pode excluir permanentemente um usuário da sua organização sem aguardar 30 dias para a exclusão automática. Um usuário excluído permanentemente não pode ser restaurado por você, por outro administrador nem pelo suporte ao cliente da Microsoft.

>[!Note]
>Se você excluir um usuário permanentemente por engano, terá que criar um novo usuário e inserir manualmente todas as informações anteriores. Para mais informações sobre como criar um novo usuário, consulte [Adicionar ou excluir usuários ](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Para excluir permanentemente um usuário

1. Na página **Usuários - Usuários excluídos**, pesquise e selecione um dos usuários disponíveis. Por exemplo, _Rae Huff_.

2. Selecione **Excluir permanentemente**.

    ![Usuários – página usuários excluídos, com a opção Excluir usuário realçada](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Próximas etapas
Depois de ter restaurado ou excluído os usuários, você pode:

- [Adicionar ou excluir usuários](add-users-azure-active-directory.md)

- [Atribuir funções aos usuários](active-directory-users-assign-role-azure-portal.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar usuários convidados de outra organização](../external-identities/what-is-b2b.md)

Para obter mais informações sobre outras tarefas de gerenciamento de usuário disponíveis, [documentação de gerenciamento de usuários do Azure ad](../enterprise-users/index.yml).

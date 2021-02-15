---
title: Exibir e atribuir permissões de função de administrador – Azure AD | Microsoft Docs
description: Agora, você pode ver e gerenciar os membros de uma função de administrador do Azure AD no portal. Para aqueles que gerenciam atribuições de função com frequência.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26217930b79b958ae86d976d06a28ba4a4852ab6
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742005"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Exibir e atribuir funções de administrador no Azure Active Directory

Agora, você pode ver e gerenciar todos os membros das funções de administrador no portal do Azure Active Directory. Se você gerencia atribuições de função com frequência, provavelmente, você preferirá essa experiência. E se você já se perguntou "O que essas funções realmente fazem?", veja uma lista detalhada das permissões de cada uma das funções de administrador do Azure AD.

## <a name="view-all-roles"></a>Exibir todas as funções

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Azure Active Directory**.

1. Selecione **funções e administradores** para ver a lista de todas as funções disponíveis.

1. Selecione as reticências à direita de cada linha para ver as permissões para a função. Selecione uma função para exibir os usuários atribuídos à função. Se você vir algo diferente da imagem a seguir, leia a observação em [exibir atribuições de funções com privilégios](#view-assignments-for-privileged-roles) para verificar se você está no PRIVILEGED Identity Management (PIM).

    ![lista de funções no portal do Azure AD](./media/manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Exibir minhas funções

É fácil exibir suas próprias permissões também. Selecione **Sua função** na página **Funções e administradores** para ver as funções atribuídas atualmente a você.

## <a name="view-assignments-for-privileged-roles"></a>Exibir atribuições para funções com privilégios

Selecione **Gerenciar no PIM** para obter funcionalidades de gerenciamento adicionais. Os Administradores de Funções com Privilégios podem alterar as atribuições “Permanentes” (sempre ativas na função) para “Qualificada” (na função somente quando elevadas). Se você não tiver Privileged Identity Management, ainda poderá selecionar **gerenciar no PIM** para se inscrever em uma avaliação. O Privileged Identity Management exige um [plano de licença P2 do Azure AD Premium](../privileged-identity-management/subscription-requirements.md).

![lista de membros de uma função de administrador](./media/manage-roles-portal/member-list.png)

Se você for um Administrador Global ou um Administrador de Funções com Privilégios, adicione ou remova membros com facilidade, filtre a lista ou selecione um membro para ver suas funções atribuídas ativas.

> [!Note]
> Se você tiver uma licença do Azure AD Premium P2 e já usar Privileged Identity Management, todas as tarefas de gerenciamento de função serão executadas no gerenciamento de identidades de privilégio e não no Azure AD.
>
> ![Funções do Azure AD gerenciadas no PIM para usuários que já usam o PIM e que têm uma licença Premium P2](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>Exibir permissões de função do usuário

Quando estiver exibindo os membros de uma função, selecione **Descrição** para ver a lista completa de permissões concedidas pela atribuição de função. A página inclui links para a documentação relevante para ajudar a orientá-lo a gerenciar as funções do diretório.

![Captura de tela que mostra a página "administrador global-descrição".](./media/manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>Baixar atribuições de função

Para baixar todas as atribuições de uma função específica, na página **funções e administradores** , selecione uma função e, em seguida, selecione **baixar atribuições de função**. Um arquivo CSV que lista as atribuições em todos os escopos para essa função é baixado.

![baixar todas as atribuições de uma função](./media/manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>Atribuir uma função

1. Entre no [portal do Azure](https://portal.azure.com) com permissões de administrador global ou de administrador de função com privilégios e selecione **Azure Active Directory**.

1. Selecione **funções e administradores** para ver a lista de todas as funções disponíveis.

1. Selecione uma função para ver suas atribuições.

    ![Captura de tela que mostra a página "atribuições de administrador de usuário" com a ação "gerenciar no PIM" selecionada.](./media/manage-roles-portal/member-list.png)

1. Selecione **Adicionar atribuições** e selecione as funções que você deseja atribuir. Selecione **Gerenciar no PIM** para obter funcionalidades de gerenciamento adicionais. Se você vir algo diferente da imagem a seguir, leia a observação em [exibir atribuições de funções com privilégios](#view-assignments-for-privileged-roles) para verificar se você está no PIM.

    ![lista de permissões para uma função de administrador](./media/manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>Próximas etapas

* Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](permissions-reference.md).
* Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).

---
title: Atribuir funções do Azure AD a usuários-Azure Active Directory
description: Saiba como conceder acesso a usuários em Azure Active Directory atribuindo funções do Azure AD.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466619"
---
# <a name="assign-azure-ad-roles-to-users"></a>Atribuir funções do Azure AD aos usuários

Agora você pode ver e gerenciar todos os membros das funções de administrador no centro de administração do Azure AD. Se você gerencia atribuições de função com frequência, provavelmente, você preferirá essa experiência. Este artigo descreve como atribuir funções do Azure AD usando o centro de administração do Azure AD.

## <a name="assign-a-role"></a>Atribuir uma função

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de administrador global ou de administrador de função privilegiada.

1. Selecione **Azure Active Directory**.

1. Selecione **funções e administradores** para ver a lista de todas as funções disponíveis.

    ![Captura de tela da página funções e administradores](./media/manage-roles-portal/roles-and-administrators.png)

1. Selecione uma função para ver suas atribuições.

    Para ajudá-lo a encontrar a função de que você precisa, o Azure AD pode mostrar subconjuntos das funções com base em categorias de função. Confira o filtro de **tipo** para mostrar apenas as funções no tipo selecionado.

1. Selecione **Adicionar atribuições** e, em seguida, selecione os usuários que você deseja atribuir a essa função.

    Se você vir algo diferente da imagem a seguir, leia a observação em [Privileged Identity Management (PIM)](#privileged-identity-management-pim) para verificar se você está usando o PIM.

    ![lista de permissões para uma função de administrador](./media/manage-roles-portal/add-assignments.png)

1. Selecione **Adicionar** para atribuir a função.

## <a name="privileged-identity-management-pim"></a>PIM (Privileged Identity Management)

Você pode selecionar **gerenciar no PIM** para recursos de gerenciamento adicionais usando o [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Os Administradores de Funções com Privilégios podem alterar as atribuições “Permanentes” (sempre ativas na função) para “Qualificada” (na função somente quando elevadas). Se você não tiver Privileged Identity Management, ainda poderá selecionar **gerenciar no PIM** para se inscrever em uma avaliação. O Privileged Identity Management exige um [plano de licença P2 do Azure AD Premium](../privileged-identity-management/subscription-requirements.md).

![Captura de tela que mostra a página "atribuições de administrador de usuário" com a ação "gerenciar no PIM" selecionada](./media/manage-roles-portal/member-list-pim.png)

Se você for um Administrador Global ou um Administrador de Funções com Privilégios, adicione ou remova membros com facilidade, filtre a lista ou selecione um membro para ver suas funções atribuídas ativas.

> [!Note]
> Se você tiver uma licença do Azure AD Premium P2 e já usar Privileged Identity Management, todas as tarefas de gerenciamento de função serão executadas no gerenciamento de identidades de privilégio e não no Azure AD.
>
> ![Funções do Azure AD gerenciadas no PIM para usuários que já usam o PIM e que têm uma licença Premium P2](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Próximas etapas

* Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções, consulte [funções internas do Azure ad](permissions-reference.md).
* Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).

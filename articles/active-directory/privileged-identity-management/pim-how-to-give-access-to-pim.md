---
title: Conceder acesso para gerenciar PIM-Azure Active Directory | Microsoft Docs
description: Saiba como permitir acesso a outros administradores para gerenciar o Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba46ee5632f7411c433e4bba29201c59c552f21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835196"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Delegar acesso a Privileged Identity Management

Para delegar acesso ao Privileged Identity Management (PIM), um administrador global pode atribuir outros usuários à função de administrador de função privilegiada. Por padrão, os administradores de segurança e os leitores de segurança têm acesso somente leitura ao Privileged Identity Management. Para conceder acesso ao Privileged Identity Management, o primeiro usuário pode atribuir outras pessoas à função de **administrador de função com privilégios** . A função de administrador de função com privilégios é necessária para gerenciar somente funções do Azure AD. Permissões de administrador de função com privilégios não são necessárias para gerenciar configurações de recursos do Azure.

> [!NOTE]
> O gerenciamento de Privileged Identity Management requer a autenticação multifator do Azure AD. Como as contas da Microsoft não podem se registrar para a autenticação multifator do Azure AD, um usuário que entra com um conta Microsoft não pode acessar Privileged Identity Management.

Certifique-se de que haja sempre pelo menos dois usuários em uma função de Administrador com Função com Privilégios, no caso de um usuário ser bloqueado ou a conta ser excluída.

## <a name="delegate-access-to-manage-pim"></a>Delegar acesso para gerenciar o PIM

1. Entre no [portal do Azure](https://portal.azure.com/).

1. No Azure AD, abra **Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **funções**.

    ![Privileged Identity Management funções do Azure AD – funções](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Selecione a função de **administrador de função com privilégios** para abrir a página Membros.

    ![Administrador com Função com Privilégios - Membros](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Selecione **Adicionar membro**  para abrir o painel **adicionar membros gerenciados** .

1. Selecione **selecionar Membros** para abrir o painel **selecionar Membros** .

    ![Administrador com Função com Privilégios - Selecionar membros](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecione um membro e clique em **Selecionar**.

1. Selecione **OK** para tornar o membro qualificado para a função de **administrador de função com privilégios** .

    Quando você atribui uma nova função a alguém em Privileged Identity Management, elas são configuradas automaticamente como **qualificadas** para ativar a função.

1. Para tornar o membro permanente, selecione o usuário na lista de membros de administrador da função com privilégios.

1. Selecione **mais** e **torne permanente** para tornar a atribuição permanente.

    ![Administrador com Função com Privilégios - Tornar permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Enviar ao usuário um link para [começar a usar Privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Remover acesso para gerenciar PIM

Antes de remover alguém da função Administrador com Função com Privilégios, sempre verifique se ainda haverá pelo menos dois usuários atribuídos a ela.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **funções**.

1. Selecione a função de **administrador de função com privilégios** para abrir a página Membros.

1. Marque a caixa de seleção ao lado do usuário que você deseja remover e, em seguida, selecione **Remover membro**.

    ![Administrador com Função com Privilégios - Remover membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Quando for solicitado que você confirme que deseja remover o membro da função, selecione **Sim**.

## <a name="next-steps"></a>Próximas etapas

- [Começar usando o Privileged Identity Management](pim-getting-started.md)

---
title: Como conceder acesso ao Privileged Identity Management - Azure | Microsoft Docs
description: "Saiba como adicionar funções a usuários com a extensão Azure Active Directory Privileged Identity Management para que possam gerenciar o PIM."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: d4c53b53-2b37-41e6-813c-96ec08a1c897
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: aeaefb484b29da6e89c2c3c650a79a881b3fa5b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>Conceder acesso para gerenciamento do Azure AD Privileged Identity Management
O administrador global que habilita o Azure AD PIM (Privileged Identity Management) para uma organização automaticamente obtém atribuições de função e acesso ao PIM. Entretanto, ninguém mais obtém acesso de gravação por padrão, incluindo outros administradores globais. Outros administradores globais, os administradores de segurança e leitores de segurança têm acesso somente leitura ao Azure AD PIM. Para fornecer acesso ao PIM, o primeiro usuário pode atribuir a outros usuários a função **Administrador com função com privilégios** . Essa atribuição deve ser feita no próprio PIM e não pode ser alterada por meio do PowerShell ou outros portais.

> [!NOTE]
> O gerenciamento do Azure AD PIM requer o Azure MFA. Como as contas da Microsoft não podem se registrar para o Azure MFA, um usuário que entra com uma conta da Microsoft não pode acessar o Azure AD PIM.
> 
> 

Certifique-se de que haja sempre pelo menos dois usuários em uma função de administrador de função com privilégios, no caso de um usuário estar bloqueado ou sua conta ser excluída.

## <a name="give-another-user-access-to-manage-pim"></a>Fornecer a outro usuário o acesso para gerenciar o PIM
1. Entre no [Portal do Azure](https://portal.azure.com/) e selecione o aplicativo **Azure AD Privileged Identity Management** no painel.
2. Selecione **Gerenciar funções com privilégios** > **Administrador com função com privilégios** > **Adicionar**.
   
    ![Adicionar administradores com função com privilégios – captura de tela][1]
3. Na folha Adicionar usuários gerenciados, a etapa 1 já foi concluída. Selecione a etapa 2, **Selecionar usuários** e pesquise o usuário que você deseja adicionar.
   
    ![Selecionar usuários – captura de tela][2]
4. Selecione o usuário nos resultados da pesquisa e clique em **Concluído**.
5. Clique em **OK** para salvar sua seleção. O usuário que você selecionou aparecerá na lista de Administradores com função com privilégios.
   
   * Sempre que você atribuir uma nova função a alguém, essa pessoa é automaticamente configurada como qualificada para ativar a função. Se você desejar torná-los permanentes na função, clique no usuário na lista. Selecione **tornar perm.** no menu de informações do usuário.
6. Enviar ao usuário um link para a [Introdução ao Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

## <a name="remove-another-users-access-rights-for-managing-pim"></a>Remover direitos de acesso de outro usuário para o gerenciamento de PIM
Antes de remover alguém da função de administrador de função com privilégios, certifique-se de que ainda haverá dois usuários atribuídos a ela.

1. No painel PIM, clique na função **Administrador com função com privilégios**.  A lista de usuários atualmente naquela função será exibida.
2. Clique no usuário na lista de usuários.
3. Clique em **Remover**.  Será exibida uma mensagem de confirmação.
4. Clique em **Sim** para remover o usuário da função.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png

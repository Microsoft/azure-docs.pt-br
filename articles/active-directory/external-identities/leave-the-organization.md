---
title: Deixar uma organização como um usuário convidado-Azure Active Directory
description: Mostra como um usuário convidado do Azure AD B2B pode deixar uma organização usando o Painel de Acesso.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc3ef7d168c17ec10fe64925adbda7044a2a4c82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87907956"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Deixar uma organização como um usuário convidado

Um usuário convidado do Azure Active Directory B2B (Azure AD) pode optar por deixar uma organização a qualquer momento se ele não precisar mais usar aplicativos da organização ou manter qualquer associação. Um usuário pode deixar uma organização por conta própria, sem a necessidade de contatar um administrador.

> [!NOTE]
> Um usuário convidado não poderá deixar uma organização se sua conta estiver desabilitada no locatário inicial ou no locatário do recurso. Se sua conta estiver desabilitada, o usuário convidado precisará entrar em contato com o administrador de locatários, que pode excluir a conta de convidado ou habilitar a conta de convidado para que o usuário possa sair da organização.

## <a name="leave-an-organization"></a>Sair da organização

Para sair de uma organização, siga estas etapas.

1. Vá para a página de perfil do painel de acesso seguindo uma das seguintes etapas:
   
   - Na [portal do Azure](https://portal.azure.com), clique em seu nome no canto superior direito e selecione **Exibir conta**.
   - Abra o [painel de acesso](https://myapps.microsoft.com), clique em seu nome no canto superior direito e, ao lado de **organizações**, selecione o ícone de configurações (engrenagem).
 
   ![Captura de tela mostrando as configurações do usuário no Painel de Acesso](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Se você ainda não tiver entrado na organização que deseja deixar, em **organizações**, clique no link **entrar para sair da organização** ao lado do nome da organização. Depois de entrar, clique em seu nome novamente no canto superior direito e, ao lado de **organizações**, selecione o ícone de configurações (engrenagem).

3. Em **Organizações**, localizar a organização que deseja sair e selecione **Deixar a organização**.

   ![Captura de tela mostrando a opção Deixar organização na interface do usuário](media/leave-the-organization/LeaveOrg.png)

4. Quando solicitado a confirmar, selecione **Deixar**. 

## <a name="account-removal"></a>Remoção de conta

Quando um usuário deixar uma organização, a conta de usuário é "excluída de forma reversível" no diretório. Por padrão, o objeto de usuário é movido para a área **usuários excluídos** no Azure AD, mas não é excluído permanentemente por 30 dias. Essa exclusão reversível permite ao administrador restaurar a conta de usuário (incluindo grupos e permissões), se o usuário faz uma solicitação para restaurar a conta dentro do período de 30 dias.

Se desejar, um administrador de locatários pode excluir permanentemente a conta a qualquer momento durante o período de 30 dias. Para fazer isso:

1. No [Portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
2. Em **Gerenciar**, selecione **Usuários**.
3. Selecione **Usuários Excluídos**.
4. Selecione a caixa de seleção próxima a um usuário deletado e então selecione **Deletar permanentemente**.

Se você permanentemente deletar um usuário, esta ação será irrevogável.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do Azure AD B2B, consulte [O que é a colaboração do Azure AD B2B?](what-is-b2b.md)




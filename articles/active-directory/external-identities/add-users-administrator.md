---
title: Adicionar usuários de colaboração B2B no portal do Azure - Azure AD
description: Mostra como um administrador pode adicionar usuários convidados ao diretório de uma organização parceira usando a colaboração B2B do Azure Active Directory (Microsoft Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2566b427777aec273df17863f06040de20649d97
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581840"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure

Como usuário atribuído a qualquer uma das funções de diretório de administrador limitadas, você pode usar o portal do Azure para convidar usuários de colaboração B2B. É possível convidar usuários convidados para o diretório, um grupo ou um aplicativo. Depois de convidar um usuário por qualquer um desses métodos, a conta do usuário convidado é adicionada ao Azure Active Directory (Microsoft Azure AD), com um tipo de usuário *Convidado*. O usuário convidado deve, então, resgata o convite para acessar recursos. Um convite de um usuário não expira.

Depois de adicionar um usuário convidado ao diretório, você poderá enviar ao usuário convidado um link direto para um aplicativo compartilhado, ou o usuário convidado poderá clicar na URL de resgate no email de convite. Para obter mais informações sobre o processo de resgate, consulte [Regaste de convite de colaboração B2B](redemption-experience.md).

> [!IMPORTANT]
> Você deve seguir as etapas em [Como: Adicionar as informações de privacidade da organização no Azure Active Directory](../fundamentals/active-directory-properties-area.md) para adicionar a URL de política de privacidade da sua organização. Como parte do processo de resgate de convite pela primeira vez, um usuário convidado deve concordar com os termos de privacidade para continuar. 

## <a name="before-you-begin"></a>Antes de começar

Verifique se as definições de colaboração externa de sua organização estão configuradas de forma que você tenha permissão para convidar participantes. Por padrão, todos os usuários e administradores podem convidar participantes. Mas as políticas de colaboração externa de sua organização podem ser configuradas para impedir que certos tipos de usuários ou administradores convidem participantes. Para saber como exibir e definir essas políticas, consulte [Habilitar colaboração externa B2B e gerenciar quem pode convidar participantes](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Adicionar usuários convidados ao diretório

Para adicionar usuários de colaboração B2B ao diretório, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) como usuário atribuído a uma função de diretório de administrador limitada ou função de Emissor do Convite.
2. Procure e selecione **Azure Active Directory** em qualquer página.
3. Em **Gerenciar**, selecione **Usuários**.
4. Selecione **Novo usuário convidado**.

   ![Mostra onde o novo usuário convidado está na interface do usuário](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
5. Na página **Novo usuário**, selecione **Convidar usuário** e adicione as informações do usuário convidado. 

    > [!NOTE]
    > Não há suporte para endereços de email de grupo; insira o endereço de email para um indivíduo. Além disso, alguns provedores de email permitem que os usuários adicionem um sinal de adição (+) de símbolo e texto adicional aos seus endereços de email para ajudar com coisas como a filtragem de caixa de entrada. No entanto, o Microsoft Azure Active Directory atualmente não dá suporte além para símbolos de adição em endereços de email. Para evitar problemas de entrega, omita o símbolo de adição e quaisquer caracteres seguintes até o símbolo @.

   - **Name.** O nome e o sobrenome do usuário convidado.
   - **Endereço de email (obrigatório)** . O endereço de email do usuário convidado.
   - **Mensagem pessoal (opcional)** Inclua uma mensagem pessoal de boas-vindas para o usuário convidado.
   - **Grupos**: você pode adicionar o usuário convidado a um ou mais grupos existentes, ou pode fazer isso posteriormente.
   - **Função do diretório**: se precisar de permissões administrativas do Azure AD para o usuário, você poderá adicioná-las a uma função do Azure AD. 

7. Selecione **Convidar** para enviar automaticamente o convite ao usuário convidado. 
 
Depois de enviar o convite, a conta de usuário é automaticamente adicionada ao diretório como convidado.


![Mostrar o usuário B2B com o tipo de usuário Convidado](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Adicionar usuários convidados a um grupo
Se você precisar adicionar manualmente usuários de colaboração B2B a um grupo, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Procure e selecione **Azure Active Directory** em qualquer página.
3. Em **Gerenciar**, selecione **Grupos**.
4. Selecione um grupo (ou clique em **Novo grupo** para criar um novo). É recomendável incluir na descrição do grupo que o grupo contém usuários convidados B2B.
5. Selecione **Membros**. 
6. Realize um dos seguintes procedimentos:
   - Se o usuário convidado já existir no diretório, procure o usuário B2B. Selecione o usuário e clique em **Selecionar** para adicioná-lo ao grupo.
   - Se o usuário convidado ainda não existir no diretório, convide-o para fazer parte do grupo digitando seu endereço de email na caixa de pesquisa, digitando uma mensagem pessoal opcional e, em seguida, clicando em **Selecionar**. O convite é enviado automaticamente para o usuário convidado.
     
     ![Adicionar botão Convidar para adicionar membros convidados](./media/add-users-administrator/GroupInvite.png)
   
Também é possível usar grupos dinâmicos com a colaboração B2B do Microsoft Azure AD. Para obter mais informações, consulte [Grupos dinâmicos e colaboração B2B do Azure Active Directory](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Adicionar usuários convidados a um aplicativo

Para adicionar usuários de colaboração B2B a um aplicativo, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Procure e selecione **Azure Active Directory** em qualquer página.
3. Em **Gerenciar**, selecione **Aplicativos empresariais** > **Todos aplicativos**.
4. Selecione o aplicativo ao qual você deseja adicionar usuários convidados.
5. No painel do aplicativo, selecione **Total de Usuários** para abrir o painel **Usuários e grupos**.

    ![Botão Total de Usuários para abrir Usuários e grupos](./media/add-users-administrator/AppUsersAndGroups.png)

6. Selecione **Adicionar usuário**.
7. Em **Adicionar Atribuição**, selecione **Usuários e grupos**.
8. Realize um dos seguintes procedimentos:
   - Se o usuário convidado já existir no diretório, procure o usuário B2B. Selecione o usuário, clique em **Selecionar** e, em seguida, clique em **Atribuir** para adicionar o usuário ao aplicativo.
   - Se o usuário convidado ainda não existir no diretório, em **Selecionar membro ou convidar um usuário externo**, digite o endereço de e-mail do usuário. Na caixa de mensagem, digite uma mensagem pessoal opcional. Na caixa de mensagem, clique em **convidar**.
           
       ![Captura de tela que realça onde adicionar o endereço de email do usuário, a mensagem personalizada e também realça o botão convidar.](./media/add-users-administrator/AppInviteUsers.png)
   
      Clique em **Selecionar** e, em seguida, clique em **Atribuir** para adicionar o usuário ao aplicativo. Um convite é enviado automaticamente para o usuário convidado.

9. O usuário convidado é exibido na lista **Usuários e grupos** do aplicativo, com a função **Acesso Padrão** atribuída. Se quiser alterar a função, faça o seguinte:
   - Selecione o usuário convidado e, em seguida, selecione **Editar**. 
   - Em **Editar Atribuição**, clique em **Selecionar Função** e selecione a função que deseja atribuir ao usuário selecionado.
   - Clique em **Selecionar**.
   - Clique em **Atribuir**.
 
## <a name="resend-invitations-to-guest-users"></a>Reenviar convites para usuários convidados

Se um usuário convidado ainda não tiver resgatado o convite, você poderá reenviar o email de convite.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Procure e selecione **Azure Active Directory** em qualquer página.
3. Em **Gerenciar**, selecione **Usuários**.
5. Selecione a conta do usuário.
6. Em **Gerenciar**, selecione **Perfil**.
7. Se o usuário ainda não tiver aceitado o convite, na seção **identidade** , o **convite aceito** será definido como **não**. Para reenviar o convite, selecione **(gerenciar)**. Em seguida, na página **gerenciar convites** , ao lado de * * reenviar convite? " Selecione **Sim** e selecione **concluído**.

> [!NOTE]
> Se você reenviar um convite que originalmente direcionou o usuário para um aplicativo específico, entenda que o link no novo convite levará o usuário ao Painel de Acesso de nível superior.
> Além disso, somente os usuários com permissões de convite poderão reenviar convites.

## <a name="next-steps"></a>Próximas etapas

- Para saber como os administradores não Microsoft Azure AD podem adicionar usuários convidados B2B, consulte [Como os operadores de informação adicionam usuários de colaboração B2B?](add-users-information-worker.md)
- Para obter informações sobre o email de convite, consulte [Os elementos do email de convite para colaboração B2Bl](invitation-email-elements.md).

---
title: Adicionar usuários de colaboração B2B como um operador de informações-Azure AD
description: A colaboração B2B permite que operadores de informações e proprietários de aplicativos adicionem usuários convidados ao Azure AD para acesso | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 345cbe2efb644fe2b84d4a6021ff962337e29285
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219905"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Como usuários na organização podem convidar usuários convidados para um aplicativo

Depois que um usuário convidado tiver sido adicionado ao diretório no Azure AD, o proprietário do aplicativo poderá enviar ao usuário convidado um link direto para o aplicativo que deseja compartilhar. Os administradores do Azure AD também podem configurar o gerenciamento de autoatendimento para aplicativos baseados em SAML ou galeria no locatário do Azure AD. Dessa forma, os proprietários de aplicativos podem gerenciar seus próprios usuários convidados, mesmo que os usuários convidados ainda não tenham sido adicionados ao diretório. Quando um aplicativo é configurado para autoatendimento, o proprietário do aplicativo usa o Painel de Acesso para convidar um usuário convidado para um aplicativo ou adicionar um usuário convidado a um grupo que tenha acesso ao aplicativo. O gerenciamento de aplicativo de autoatendimento para galeria e aplicativos baseados em SAML requer uma configuração inicial por um administrador. Veja a seguir um resumo das etapas de instalação (para obter instruções mais detalhadas, consulte [pré-requisitos](#prerequisites) mais adiante nesta página):

 - Habilitar gerenciamento de grupos de autoatendimento para o locatário
 - Criar um grupo para atribuir ao aplicativo e tornar o usuário um proprietário
 - Configurar o aplicativo para autoatendimento e atribuir o grupo ao aplicativo

> [!NOTE]
> * Este artigo descreve como configurar o gerenciamento de autoatendimento para aplicativos baseados em SAML e galeria que você adicionou ao locatário do Azure AD. Você também pode [configurar grupos de autoatendimento Microsoft 365](../enterprise-users/groups-self-service-management.md) para que os usuários possam gerenciar o acesso a seus próprios grupos de Microsoft 365. Para obter mais maneiras como os usuários podem compartilhar arquivos e aplicativos do Office com usuários convidados, consulte [acesso de convidado em grupos de Microsoft 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) e [Compartilhe arquivos ou pastas do SharePoint](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).
> * Os usuários só poderão convidar convidados se tiverem a função de **convite do convidado** .
## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Convidar um usuário convidado a um aplicativo do Painel de Acesso

Depois que um aplicativo é configurado para autoatendimento, os proprietários de aplicativos podem usar o próprio Painel de Acesso para convidar um usuário convidado para o aplicativo que deseja compartilhar. O usuário convidado não precisa necessariamente ser adicionado ao Azure AD com antecedência. 

1. Abra o Painel de Acesso, indo para `https://myapps.microsoft.com`.
2. Aponte para o aplicativo, selecione as reticências (**...**) e, em seguida, selecione **Gerenciar aplicativo**.
 
   ![Captura de tela mostrando o submenu gerenciar aplicativo para o aplicativo Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. Na parte superior da lista de usuários, selecione **+** .
   
   ![Captura de tela mostrando o símbolo de adição para adicionar membros ao aplicativo](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Na caixa de pesquisa **Adicionar membros**, insira o endereço de email do usuário convidado. Opcionalmente, inclua uma mensagem de boas-vindas.
   
   ![Captura de tela mostrando a janela Adicionar membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
5. Selecione **Adicionar** para enviar um convite ao usuário convidado. Depois de enviar o convite, a conta de usuário é automaticamente adicionada ao diretório como convidado.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Convidar alguém para ingressar em um grupo que tenha acesso ao aplicativo
Depois que um aplicativo é configurado para autoatendimento, os proprietários de aplicativos podem convidar usuários convidados para os grupos que gerenciam e que tenham acesso aos aplicativos que desejam compartilhar. Os usuários convidados não precisam existir no diretório. O proprietário do aplicativo segue essas etapas para convidar um usuário convidado para o grupo, de modo que ele possa acessar o aplicativo.

1. Certifique-se de que você é um proprietário do grupo de autoatendimento com acesso ao aplicativo que deseja compartilhar.
2. Abra o Painel de Acesso, indo para `https://myapps.microsoft.com`.
3. Selecione o aplicativo de **Grupos**.
   
   ![Captura de tela mostrando o aplicativo grupos no painel de acesso](media/add-users-iw/access-panel-groups.png)
   
4. Em **Grupos que possuo**, selecione o grupo que tem acesso ao aplicativo que você deseja compartilhar.
   
   ![Captura de tela mostrando onde selecionar um grupo nos grupos dos quais sou proprietário](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Na parte superior da lista de membros do grupo, selecione **+** .
   
   ![Captura de tela mostrando o símbolo de adição para adicionar membros ao grupo](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Na caixa de pesquisa **Adicionar membros**, insira o endereço de email do usuário convidado. Opcionalmente, inclua uma mensagem de boas-vindas.
   
   ![Captura de tela mostrando a janela Adicionar membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
7. Selecione **Adicionar** para enviar automaticamente o convite ao usuário convidado. Depois de enviar o convite, a conta de usuário é automaticamente adicionada ao diretório como convidado.


## <a name="prerequisites"></a>Pré-requisitos

O gerenciamento de aplicativos de autoatendimento exige alguma configuração inicial por um administrador global e um administrador do Azure AD. Como parte dessa configuração, você configurará o aplicativo para autoatendimento e atribuirá um grupo ao aplicativo que o proprietário do aplicativo poderá gerenciar. Também é possível configurar o grupo para permitir que alguém solicite a associação, mas isso exige a aprovação de um proprietário do grupo. (Saiba mais sobre [gerenciamento de grupos de autoatendimento](../enterprise-users/groups-self-service-management.md).) 

> [!NOTE]
> Não é possível adicionar usuários convidados a um grupo dinâmico ou a um grupo que esteja sincronizado com o Active Directory local.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Habilitar gerenciamento de grupos de autoatendimento para o locatário
1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Selecione **Grupos**.
4. Em **Configurações**, selecione **Geral**.
5. Em **Gerenciamento de Grupos de Autoatendimento**, próximo a **Proprietários podem gerenciar solicitações de associação ao grupo no Painel de Acesso**, selecione **Sim**.
6. Clique em **Salvar**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Criar um grupo para atribuir ao aplicativo e tornar o usuário um proprietário
1. Entre no [portal do Azure](https://portal.azure.com) como um administrador do Azure AD ou Administrador Global.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Selecione **Grupos**.
4. Selecione **Novo grupo**.
5. Em **Tipo de grupo**, selecione **Segurança**.
6. Digite o **Nome do grupo** e a **Descrição do grupo**.
7. Em **Tipo de associação**, selecione **Atribuído**.
8. Selecione **Criar** e feche a página **Grupo**.
9. Na página **Grupos - Todos os grupos**, abra o grupo. 
10. Em **Gerenciar**, selecione **Proprietários** > **Adicionar proprietários**. Pesquise o usuário que deve gerenciar o acesso ao aplicativo. Selecione o usuário e, em seguida, clique em **Selecionar**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Configurar o aplicativo para autoatendimento e atribuir o grupo ao aplicativo
1. Entre no [portal do Azure](https://portal.azure.com) como um administrador do Azure AD ou Administrador Global.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **Gerenciar**, selecione **Aplicativos empresariais** > **Todos aplicativos**.
4. Na lista de aplicativos, localize e abra o aplicativo.
5. Em **Gerenciar**, selecione **Logon único**, e configure o aplicativo para logon único. (Para detalhes, consulte [como gerenciar logon único para aplicativos empresariais](../manage-apps/add-application-portal-setup-sso.md).)
6. Em **Gerenciar**, selecione **Autoatendimento** e configure o acesso ao aplicativo de autoatendimento. (Para detalhes, consulte [como usar o acesso ao aplicativo de autoatendimento](../manage-apps/access-panel-manage-self-service-access.md).) 

    > [!NOTE]
    > Para a configuração **Para qual grupo os usuários atribuídos devem ser adicionados?**, selecione o grupo criado na seção anterior.
7. Em **Gerenciar**, selecione **Usuários e Grupos** e verifique se o grupo de autoatendimento que você criou aparece na lista.
8. Para adicionar o aplicativo ao painel de acesso do proprietário do grupo, selecione **Adicionar**  >  **usuários e grupos** do usuário. Pesquise o proprietário do grupo e selecione o usuário, clique em **Selecionar** e, em seguida, clique em **Atribuir** para adicionar o usuário ao aplicativo.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](add-users-administrator.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Preço de identidades externas](external-identities-pricing.md)
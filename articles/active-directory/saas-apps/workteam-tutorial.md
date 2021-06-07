---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Workteam | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workteam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.openlocfilehash: 077af1e8add63aac9f3d866c5d665ad4a47b133f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520040"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Workteam

Neste tutorial, você aprenderá a integrar o Workteam ao Azure AD (Azure Active Directory). Ao integrar o Workteam ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Workteam.
* Permitir que os usuários sejam conectados automaticamente ao Workteam com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Workteam habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Workteam dá suporte ao SSO iniciado por **SP e IdP**

## <a name="adding-workteam-from-the-gallery"></a>Adicionando o Workteam da galeria

Para configurar a integração do Workteam ao Azure AD, você precisa adicionar o Workteam da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Workteam** na caixa de pesquisa.
1. Selecione **Workteam** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>Configurar e testar o logon único do Azure AD para o Workteam

Configure e teste o SSO do Azure AD com o Workteam usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Workteam.

Para configurar e testar o SSO do Azure AD com o Workteam, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Workteam](#configure-workteam-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Workteam](#create-workteam-test-user)** – para ter um equivalente de B.Fernandes no Workteam que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Workteam**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado no modo iniciado do **IDP** e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.workte.am`

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Workteam**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Workteam.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Workteam**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-workteam-sso"></a>Configurar o SSO do Workteam

1. Para automatizar a configuração no Workteam, é necessário instalar a **Extensão do navegador Entrada Segura de Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, um clique em **Configurar o Workteam** direcionará você ao aplicativo Workteam. Nele, forneça as credenciais de administrador para entrar no Workteam. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Caso deseje configurar o Workteam manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Workteam como administrador e execute as seguintes etapas:

4. No canto superior direito, clique no **logotipo do perfil** e, em seguida, em **Configurações da organização**. 

    ![Configurações do Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. Na seção **AUTENTICAÇÃO**, clique no **Logotipo de configurações**.

     ![Workteam Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. Na página **Configurações de SAML** , execute as seguintes etapas:

     ![Workteam SAML](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Selecione o **IdP do SAML** como **Azure AD**.

    b. Na caixa de texto **URL de Logon Único do SAML**, cole o valor da **URL de Logon**, copiado do portal do Azure.

    c. Na caixa de texto **ID da Entidade SAML**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    d. No Bloco de Notas, abra o **certificado codificado em Base64** baixado do portal do Azure, copie seu conteúdo e cole-o na caixa **Certificado de Autenticação SAML (Base64)**.

    e. Clique em **OK**.

### <a name="create-workteam-test-user"></a>Criar usuário de teste do Workteam

Para permitir que os usuários do Azure AD entrem no Workteam, eles precisam ser provisionados no Workteam. No Workteam, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no Workteam como Administrador de Segurança.

2. Na parte superior central da página **Configurações da organização**, clique em **USUÁRIOS** e, em seguida, em **NOVO USUÁRIO**.

    ![Usuário do Workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Na página **Novo funcionário**, realize as seguintes etapas:

    ![Novo usuário do Workteam](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Na caixa de texto **Nome**, insira o nome do usuário como **B.Fernandes**.

    b. Na caixa de texto **Email**, insira o email do usuário como `B.Simon\@contoso.com`.

    c. Clique em **OK**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Workteam no Painel de Acesso, você deverá ser conectado automaticamente ao Workteam para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Workteam com o Azure AD](https://aad.portal.azure.com/)
---
title: 'Tutorial: Integração do Azure Active Directory com o InsideView | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o logon único entre o Azure Active Directory e o InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 2149b8410104b39652b176895a31b42e094265f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100100"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração do Azure Active Directory com o InsideView

Neste tutorial, você aprenderá a integrar o InsideView ao Azure AD (Azure Active Directory).
Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao InsideView.
* Você pode permitir que os usuários sejam conectados automaticamente ao InsideView (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao InsideView, você precisará ter:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada do InsideView que tenha o logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O InsideView é compatível com o SSO iniciado por IdP.

## <a name="add-insideview-from-the-gallery"></a>Adicionar o InsideView por meio da galeria

Para configurar a integração do InsideView ao Azure AD, você precisará adicionar o InsideView por meio da galeria à lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**:

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **InsideView**. Selecione **InsideView** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

    ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o InsideView usando um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no InsideView.

Para configurar e testar o logon único do Azure AD com o InsideView, você precisará concluir estas etapas:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do InsideView](#configure-insideview-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Criar um usuário de teste do InsideView](#create-an-insideview-test-user)** que esteja vinculado à representação do usuário do Azure AD.
6. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o InsideView, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do InsideView, selecione **Logon único**:

    ![Selecionar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica do SAML**, execute as etapas a seguir.

    ![Caixa de diálogo Configuração Básica de SAML](common/idp-reply.png)

    Na caixa **URL de Resposta**, digite uma URL neste padrão:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Esse valor é um espaço reservado. Você precisa usar a URL de resposta real. Contate a [equipe de suporte do InsideView](mailto:support@insideview.com) para obter o valor. Você também pode ver os padrões exibidos na caixa de diálogo **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado de **Certificado (Bruto)** , de acordo com suas necessidades e salve o certificado no computador:

    ![Link de download do certificado](common/certificateraw.png)

6. Na seção **Configurar o InsideView**, copie as URLs apropriadas de acordo com suas necessidades:

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    1. **Identificador do Azure AD**.

    1. **URL de Logoff**.

### <a name="configure-insideview-single-sign-on"></a>Configurar o logon único do InsideView

1. Em uma nova janela do navegador da Web, entre em seu site de empresa do InsideView como administrador.

1. Na parte superior da janela, selecione **Admin** (Administrador), **SingleSignOn Settings** (Configurações de Logon Único) e, em seguida, **Add SAML** (Adicionar SAML).
   
   ![Configurações de logon único do SAML](./media/insideview-tutorial/ic794135.png "SAML single sign-on settings")

1. Na seção **Add a New SAML** (Adicionar um Novo SAML), execute as etapas a seguir.

    ![Seção Adicionar um Novo SAML](./media/insideview-tutorial/ic794136.png "Add a New SAML section")

    1. Na caixa **STS Name** (Nome do STS), insira um nome para a configuração.

    1. Na caixa **SamlP/WS-Fed Unsolicited EndPoint** (Ponto de Extremidade Não Solicitado do SAML-P/WS-Fed), cole o valor da **URL de Logon** copiado do portal do Azure.

    1. Abra o certificado Bruto que você baixou do portal do Azure. Copie o conteúdo do certificado para a área de transferência e, em seguida, cole o conteúdo na caixa **STS Certificate** (Certificado STS).

    1. Na caixa **Crm User Id Mapping** (Mapeamento de ID de Usuário do CRM), insira **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. Na caixa **Crm Email Mapping** (Mapeamento de Email do CRM), insira **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. Na caixa **Crm First Name Mapping** (Mapeamento de Nome do CRM), insira **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** .

    1. Na caixa de texto **Crm lastName Mapping** (Mapeamento de Sobrenome do CRM), insira **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** .  

    1. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste chamado Brenda Fernandes no portal do Azure.

1. No portal do Azure, selecione **Azure Active Directory** no painel esquerdo, **Usuários** e, em seguida, **Todos os usuários**:

    ![Selecionar Todos os usuários](common/users.png)

2. Selecione **Novo usuário** na parte superior da janela:

    ![Selecionar Novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, execute as etapas a seguir.

    ![Caixa de diálogo Usuário](common/user-properties.png)

    1. Na caixa **Nome**, insira **BrendaFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **BrendaFernandes@\<domíniodaempresa>.\<extensão>** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **Mostrar Senha** e, em seguida, anote o valor mostrado na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao InsideView.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **InsideView**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **InsideView**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da janela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da janela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-an-insideview-test-user"></a>Criar um usuário de teste do InsideView

Para permitir que os usuários do Azure AD entrem no InsideView, você precisará adicioná-los ao InsideView. Você precisará adicioná-los manualmente.

Para criar usuários ou contatos no InsideView, contate a [equipe de suporte do InsideView](mailto:support@insideview.com).

> [!NOTE]
> Você pode usar qualquer ferramenta de criação de conta de usuário ou API fornecida pelo InsideView para provisionar as contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do InsideView no Painel de Acesso, você deverá ser conectado automaticamente à instância do InsideView, para a qual você configurou o SSO. Para obter mais informações sobre o Painel de Acesso, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

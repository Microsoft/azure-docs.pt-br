---
title: 'Tutorial: Integração do Azure Active Directory ao Ziflow | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: d9745bdb1cb6de86a96946564865958433d49732
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086205"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Tutorial: Integração do Azure Active Directory ao Ziflow

Neste tutorial, você aprende a integrar o Ziflow ao Azure AD (Azure Active Directory).
A integração do Ziflow ao Microsoft Azure AD oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure AD quem terá acesso ao Ziflow.
* Você pode permitir que os usuários sejam conectados automaticamente ao Ziflow (Logon Único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com o Ziflow, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do Ziflow habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Ziflow é compatível com o SSO iniciado por **SP**

## <a name="adding-ziflow-from-the-gallery"></a>Adição do Ziflow da galeria

Para configurar a integração do Ziflow ao Microsoft Azure AD, você precisará adicionar o Ziflow da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Ziflow da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Ziflow**, selecione **Ziflow** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Ziflow na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Ziflow, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Ziflow.

Para configurar e testar o logon único do Microsoft Azure AD com o Ziflow, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Ziflow](#configure-ziflow-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Ziflow](#create-ziflow-test-user)** – para ter um equivalente de Brenda Fernandes no Ziflow que esteja vinculado à representação de usuário do Microsoft Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure AD com o Ziflow, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Ziflow**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Ziflow](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Os valores anteriores não são reais. Você atualizará o valor de ID exclusiva no identificador e na URL de logon com o valor real, que é explicado no tutorial posteriormente.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Ziflow**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-ziflow-single-sign-on"></a>Configurar o logon único do Ziflow

1. Em uma janela diferente do navegador da Web, entre no Ziflow como Administrador de segurança.

2. Clique em Avatar no canto superior direito e, em seguida, clique em **Gerenciar conta**.

    ![Gerenciar configuração do Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. No canto superior esquerdo, clique em **Logon Único**.

    ![Logon único nas configuração do Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Na página **Logon Único**, execute as seguintes etapas:

    ![Logon único nas configuração do Ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Selecione **Tipo** como **SAML 2.0**.

    b. Na caixa de texto **URL de Entrada**, cole o valor de **URL de Logon**, copiado do portal do Azure.

    c. Carregue o certificado codificado em base 64 que você baixou do Portal do Azure, para o **Certificado de Autenticação do X509**.

    d. Na caixa de texto **URL de Saída**, cole o valor de **URL de Logoff**, copiado do portal do Azure.

    e. Da seção **Definições de configuração para seu provedor de identificador**, copie o valor de ID exclusiva destacado e acrescente-o ao identificador e à URL de logon na **Configuração Básica de SAML** no portal do Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Ziflow.

1. No portal do Azure, escolha **Aplicativos empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **Ziflow**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Ziflow**.

    ![O link do Ziflow na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-ziflow-test-user"></a>Criar um usuário de teste no Ziflow

Para permitir que os usuários do Microsoft Azure AD se conectem ao Ziflow, eles precisam estar provisionados nele. No Ziflow, o provisionamento é uma tarefa manual.

Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Entre no Ziflow como Administrador de Segurança.

2. Navegue até **Pessoas** na parte superior.

    ![Configuração pessoas do Ziflow](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Clique em **Adicionar** e depois em **Adicionar usuário**.

    ![Configuração adicionar usuários do Ziflow](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Na janela pop-up **Adicionar um Usuário**, realize as seguintes etapas:

    ![Configuração adicionar usuários do Ziflow](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Na caixa de texto **Email**, insira o email do usuário como brittasimon@contoso.com.

    b. Na caixa de texto **Nome**, insira o nome do usuário como Brenda.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como Fernandes.

    d. Selecione sua função no Ziflow.

    e. Clique em **Adicionar 1 usuário**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Ziflow no Painel de Acesso, você deverá ser conectado automaticamente ao Ziflow para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


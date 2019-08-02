---
title: 'Tutorial: Integração do Azure Active Directory ao SkyDesk Email | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SkyDesk Email.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: faaa5dcc435452d6ed9e0f2c5b481df1e352dfd2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090439"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Tutorial: Integração do Azure Active Directory ao SkyDesk Email

Neste tutorial, você aprende a integrar o SkyDesk Email ao Azure AD (Azure Active Directory).
A integração do SkyDesk Email ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao SkyDesk Email.
* Você pode permitir que os usuários sejam conectados automaticamente ao SkyDesk Email (logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SkyDesk Email, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do SkyDesk Email habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SkyDesk Email dá suporte ao SSO iniciado por **SP**

## <a name="adding-skydesk-email-from-the-gallery"></a>Adicionar o SkyDesk Email a partir da galeria

Para configurar a integração do SkyDesk Email ao Azure AD, você precisará adicionar o SkyDesk Email da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SkyDesk Email a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SkyDesk Email**, selecione **SkyDesk Email** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SkyDesk Email na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o SkyDesk Email, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SkyDesk Email.

Para configurar e testar o logon único do Azure AD com o SkyDesk Email, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do SkyDesk Email](#configure-skydesk-email-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SkyDesk Email](#create-skydesk-email-test-user)** – para ter um equivalente de Brenda Fernandes no SkyDesk Email que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SkyDesk Email, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SkyDesk Email**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e de URLs do SkyDesk Email](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do SkyDesk Email](https://www.skydesk.jp/apps/support/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o SkyDesk Email**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-skydesk-email-single-sign-on"></a>Configurar o logon único SkyDesk Email

1. Em um navegador da Web diferente, entre na sua conta do SkyDesk Email como administrador.

1. No menu na parte superior, clique em **Configuração** e selecione **Organização**.

    ![Configurar o logon único](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Clique em **Domínios** no painel esquerdo.

    ![Configurar o logon único](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Clique em **Adicionar Domínio**.

    ![Configurar o logon único](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Insira seu nome de Domínio e verifique o Domínio.

    ![Configurar o logon único](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Clique em **Autenticação SAML** no painel esquerdo.

    ![Configurar o logon único](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na página de diálogo **Autenticação SAML** , realize as seguintes etapas:

    ![Configurar o logon único](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Para usar a autenticação baseada em SAML, você deve configurar um **domínio verificado** ou uma **URL de portal**. Você pode definir a URL de portal com o nome exclusivo.

    ![Configurar o logon único](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    c. A **URL de Alteração de Senha** é opcional, portanto, deixe-a em branco.

    d. Clique em **Obter Chave de Arquivo** para selecionar o certificado baixado no portal do Azure e, em seguida, clique em **Abrir** para carregar o certificado.

    e. Para **Algoritmo**, selecione **RSA**.

    f. Clique em **Ok** para salvar as alterações.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SkyDesk Email.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SkyDesk Email**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione  **Email**.

    ![O link do SkyDesk Email na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-skydesk-email-test-user"></a>Criar um usuário de teste do SkyDesk Email

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SkyDesk Email.

Clique em **Acesso de Usuário** no painel esquerdo do SkyDesk Email e digite seu nome de usuário.

![Configurar o logon único](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Se você precisar criar usuários em massa, contate a [equipe de suporte ao Cliente do SkyDesk Email](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SkyDesk Email no Painel de Acesso, você deverá ser conectado automaticamente ao SkyDesk Email no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


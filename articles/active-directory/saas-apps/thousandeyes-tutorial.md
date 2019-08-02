---
title: 'Tutorial: Integração do Azure Active Directory com o ThousandEyes | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: bb78b014ffe2d40b9a61da8e47893056e435ddc6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088652"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutorial: Integração do Azure Active Directory com o ThousandEyes

Neste tutorial, você aprenderá a integrar o ThousandEyes ao Azure AD (Azure Active Directory).
A integração do ThousandEyes com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao ThousandEyes.
* Você pode permitir que os usuários sejam conectados automaticamente ao ThousandEyes (Logon Único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ThousandEyes, serão necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do ThousandEyes habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ThousandEyes é compatível com SSO iniciado por **SP**

* O ThousandEyes é compatível com [provisionamento de usuário **Automatizado**](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

## <a name="adding-thousandeyes-from-the-gallery"></a>Adicionar o ThousandEyes da galeria

Para configurar a integração do ThousandEyes com o Azure AD, é necessário adicionar o ThousandEyes da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ThousandEyes da galeria, siga as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ThousandEyes**, selecione **ThousandEyes** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![ThousandEyes na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ThousandEyes, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ThousandEyes.

Para configurar e testar o logon único do Azure AD com o ThousandEyes, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do ThousandEyes](#configure-thousandeyes-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do ThousandEyes](#create-thousandeyes-test-user)** – para ter um equivalente de Brenda Fernandes no ThousandEyes que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ThousandEyes, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ThousandEyes**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de URLs e Domínio do ThousandEyes](common/sp-signonurl.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.thousandeyes.com/login/sso`

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o ThousandEyes**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-thousandeyes-single-sign-on"></a>Configurar o logon único do ThousandEyes

1. Em outra janela do navegador da Web, entre em seu site de empresa do **ThousandEyes** como administrador.

2. No menu na parte superior, clique em **Configurações**.

    ![Configurações](./media/thousandeyes-tutorial/ic790066.png "Configurações")

3. Clique em **Conta**

    ![Conta](./media/thousandeyes-tutorial/ic790067.png "Conta")

4. Clique na guia **Segurança e Autenticação**.

    ![Segurança e autenticação](./media/thousandeyes-tutorial/ic790068.png "Segurança e autenticação")

5. Na seção **Configurações de Logon Único** , realize as seguintes etapas:

    ![Configurar o logon único](./media/thousandeyes-tutorial/ic790069.png "Configurar o logon único")

    a. Selecione **Habilitar Logon Único**.

    b. Na caixa de texto **URL da Página de Logon**, cole a **URL de Logon** copiada do portal do Azure.

    c. Na caixa de texto **URL da Página de Logoff**, cole a **URL de Logoff** copiada do portal do Azure.

    d. Na caixa de texto **Emissor do Provedor de Identidade**, cole o **Identificador do Azure AD** copiado do portal do Azure.

    e. Em **Certificado de Verificação**, clique em **Escolher Arquivo** e carregue o certificado que você baixou do Portal do Azure.

    f. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao ThousandEyes.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **ThousandEyes**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **ThousandEyes**.

    ![O link do ThousandEyes na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-thousandeyes-test-user"></a>Criar usuário de teste do ThousandEyes

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ThousandEyes. O ThousandEyes dá suporte ao provisionamento automático do usuário, que está habilitado por padrão. Você pode encontrar [aqui](thousandeyes-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Entre em seu site empresarial do ThousandEyes como um administrador.

2. Clique em **Configurações**.

    ![Configurações](./media/thousandeyes-tutorial/IC790066.png "Configurações")

3. Clique em **Conta**.

    ![Conta](./media/thousandeyes-tutorial/IC790067.png "Conta")

4. Clique na guia **Contas e Usuários**.

    ![Contas e usuários](./media/thousandeyes-tutorial/IC790073.png "Contas e usuários")

5. Na seção **Adicionar Usuários e Contas**, realize as seguintes etapas:

    ![Adicionar contas de usuário](./media/thousandeyes-tutorial/IC790074.png "Adicionar contas de usuário")

    a. Na caixa de texto **Nome**, digite o nome do usuário como **Brenda Fernandes**.

    b. Na caixa de texto **Email**, digite o email do usuário como brittasimon@contoso.com.

    b. Clique em **Adicionar Novo Usuário à Conta**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory receberá um email com um link para confirmar e ativar a conta.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do ThousandEyes ou as APIs fornecidas pelo ThousandEyes para provisionar as contas de usuário do Azure Active Directory.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ThousandEyes no Painel de Acesso, você deverá ser conectado automaticamente ao ThousandEyes para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar Provisionamento de Usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

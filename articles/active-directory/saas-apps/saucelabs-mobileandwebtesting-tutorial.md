---
title: 'Tutorial: Integração do Azure Active Directory com o Sauce Labs - Mobile and Web Testing | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sauce Labs - Mobile e Web Testing.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 8933cb90672e49305cd0fb7dc5e4f8f04f94093e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091551"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Tutorial: Integração do Azure Active Directory com o Sauce Labs - Mobile and Web Testing

Neste tutorial, você aprenderá como integrar o Sauce Labs - Mobile e Web Testing ao Azure AD (Azure Active Directory).
A integração do Sauce Labs - Mobile e Web Testing com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Sauce Labs - Mobile e Web Testing.
* Você pode permitir que seus usuários entrem automaticamente no Sauce Labs – Mobile and Web Testing (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Sauce Labs - Mobile e Web Testing, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Sauce Labs – Mobile and Web Testing

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Sauce Labs – Mobile and Web Testing dá suporte a SSO iniciado por **IDP**
* O Sauce Labs – Mobile and Web Testing dá suporte a provisionamento de usuário **Just In Time**

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Adicionando Sauce Labs - Mobile e Web Testing na galeria

Para configurar a integração do Sauce Labs - Mobile e Web Testing no Azure AD, você precisa adicionar o Sauce Labs - Mobile e Web Testing da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Sauce Labs - Mobile e Web Testing da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Sauce Labs - Mobile e Web Testing**, selecione **Sauce Labs - Mobile e Web Testing** no painel de resultados e clique no botão **Add** para adicionar o aplicação.

    ![Sauce Labs - Mobile e Web Testing na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Sauce Labs – Mobile and Web Testing com base em um usuário de teste chamado **Brenda Fernandes**.
Para o logon único funcionar, um relacionamento de link entre um usuário do Azure AD e o usuário relacionado no Sauce Labs – Mobile and Web Testing precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o Sauce Labs - Mobile e Web Testing, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar Logon Único do Sauce Labs – Mobile and Web Testing](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Crie usuário de teste do Sauce Labs – Mobile and Web Testing](#create-sauce-labs---mobile-and-web-testing-test-user)** – para ter um equivalente de Brenda Fernandes no Sauce Labs – Mobile and Web Testing vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Sauce Labs – Mobile and Web Testing, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **Sauce Labs – Mobile and Web Testing**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Sauce Labs - Mobile e Web Testing Informações de logon único de domínio e URLs](common/preintegrated.png)

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Sauce Labs – Mobile and Web Testing**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Configurar Logon Único do Sauce Labs – Mobile and Web Testing

1. Em uma janela diferente do navegador da Web, faça login no seu site da empresa Sauce Labs - Mobile e Web Testing como administrador.

2. Clique no **ícone do usuário** e selecione a guia **Gerenciamento de equipe**.

    ![Configurar o logon único](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Digite seu **nome de domínio** na caixa de texto.

    ![Configurar o logon único](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Clique na guia **configurar**.

    ![Configurar o logon único](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Na seção **Configurar Logon Único**, execute as etapas a seguir.

    ![Configurar o logon único](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Clique em **Procurar** e carregue o arquivo de metadados baixado do AD do Azure.

    b. Selecione a caixa de seleção **PERMITE O PROVISIONAMENTO JUST-IN-TIME**.

    c. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Sauce Labs - Mobile e Web Testing.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Sauce Labs – Mobile and Web Testing**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Sauce Labs - Mobile e Web Testing**.

    ![O link do Sauce Labs - Mobile e Web Testing na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Criar usuário de teste do Sauce Labs – Mobile and Web Testing

Nesta seção, é criado um usuário chamado Brenda Fernandes no Sauce Labs – Mobile and Web Testing. O Sauce Labs – Mobile and Web Testing dá suporte a provisionamento de usuário Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir nos Sauce Labs – Mobile and Web Testing, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, entre em contato com a  [equipe de suporte do Sauce Labs - Mobile and Web Testing](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Sauce Labs – Mobile and Web Testing no Painel de Acesso, você deverá entrar automaticamente no Sauce Labs – Mobile and Web Testing para o qual configurou SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


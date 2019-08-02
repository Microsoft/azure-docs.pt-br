---
title: 'Tutorial: Integração do Azure Active Directory ao Help Scout | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Help Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3625a6bbf22a2d51546a91e8a5f7c58b7fbb264f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101182"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: Integração do Azure Active Directory ao Help Scout

Neste tutorial, você aprenderá a integrar o Help Scout ao Azure AD (Azure Active Directory).
A integração do Help Scout ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Help Scout.
* Você pode permitir que os usuários sejam conectados automaticamente ao Help Scout (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Help Scout, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Help Scout

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Help Scout dá suporte ao SSO iniciado por **SP e IDP**
* O Help Scout dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-help-scout-from-the-gallery"></a>Adicionar o Help Scout por meio da galeria

Para configurar a integração do Help Scout com o Azure AD, você precisará adicionar o Help Scout à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Help Scout por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Help Scout**, selecione **Help Scout** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Help Scout na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Help Scout, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Help Scout.

Para configurar e testar o logon único do Azure AD com o Help Scout, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Help Scout](#configure-help-scout-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Help Scout](#create-help-scout-test-user)** – para ter um equivalente de Brenda Fernandes no Help Scout que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Help Scout, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Help Scout**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do Help Scout](common/idp-intiated.png)

    a. **Identificador** é o **URI do Público-alvo (ID da Entidade do Provedor de Serviços)** do Help Scout e começa com `urn:`

    b. **URL de Resposta** é a **URL de Postback (URL do Serviço do Consumidor de Declaração)** do Help Scout e começa com `https://` 

    > [!NOTE]
    > Os valores nessas URLs são apenas para demonstração. Atualize esses valores com a URL de Resposta e o Identificador reais. Você obtém esses valores da guia **Logon único** na seção de Autenticação, que é explicada posteriormente no tutorial.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Help Scout](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite uma URL como: `https://secure.helpscout.net/members/login/`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Help Scout**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-help-scout-single-sign-on"></a>Configurar o logon único do Help Scout

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do Help Scout como um administrador.

2. Clique em **Gerenciar** no menu superior e, em seguida, selecione **Empresa** no menu suspenso.

    ![Configurar o logon único](./media/helpscout-tutorial/settings1.png)

3. Selecione **Autenticação** no painel de navegação à esquerda.

    ![Configurar o logon único](./media/helpscout-tutorial/settings2.png)

4. Isso direciona-o à seção de configurações do SAML e executa as seguintes etapas:

    ![Configurar o logon único](./media/helpscout-tutorial/settings3.png)

    a. Copie o valor da **URL de Postback (URL do Serviço do Consumidor de Declaração)** e cole o valor na caixa de texto **URL de Resposta** na seção **Configuração Básica do SAML** no portal do Azure.

    b. Copie o valor do **URI do Público-alvo (ID da Entidade do Provedor de Serviços)** e cole-o na caixa de texto **Identificador** na seção **Configuração Básica do SAML** no portal do Azure.

5. Ative a opção **Habilitar SAML** e execute as seguintes etapas:

    ![Configurar o logon único](./media/helpscout-tutorial/settings4.png)

    a. Na caixa de texto **URL de Logon Único**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Clique em **Carregar Certificado** para carregar o **Certificado(Base64)** baixado no portal do Azure.

    c. Insira os domínios de email de sua organização, por exemplo: `contoso.com` na caixa de texto **Domínios de Email**. Você pode separar múltiplos domínios com uma vírgula. A qualquer momento um Administrador ou Usuário do Help Scout que insere esse domínio específico na [Página de logon do Help Scout](https://secure.helpscout.net/members/login/) serão roteados para o Provedor de Identidade para autenticar com suas credenciais.

    d. Por fim, você pode alternar **Forçar Logon SAML** se desejar que os Usuários façam logon somente no Help Scout por meio desse método. Se você ainda desejar deixar a opção para que eles possam entrar com suas credenciais do Help Scout, pode a deixar desativada. Mesmo se estiver habilitada, um Proprietário de Conta sempre será capaz de fazer logon no Help Scout com a senha da conta.

    e. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Help Scout.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Help Scout**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Help Scout**.

    ![O link do Help Scout na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-help-scout-test-user"></a>Criar um usuário de teste do Help Scout

Nesta seção, um usuário chamado Brenda Fernandes será criado no Help Scout. O Help Scout dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Help Scout, um novo será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Help Scout no Painel de Acesso, você deverá ser conectado automaticamente ao Help Scout, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
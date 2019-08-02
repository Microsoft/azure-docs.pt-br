---
title: 'Tutorial: Integração do Azure Active Directory ao KnowledgeOwl | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: ab80b6efef71c71feea1359112d09bae90a7ab84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098880"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Tutorial: Integração do Azure Active Directory ao KnowledgeOwl

Neste tutorial, você aprende a integrar o KnowledgeOwl ao Azure AD (Azure Active Directory).
A integração do KnowledgeOwl ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você poderá controlar quem tem acesso ao KnowledgeOwl.
* Você pode permitir que os usuários sejam conectados automaticamente ao KnowledgeOwl (logon único) com suas contas do Microsoft Azure Active Directory.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao KnowledgeOwl, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura habilitada para logon único do KnowledgeOwl

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O KnowledgeOwl dá suporte ao SSO iniciado por **SP e IDP**
* O KnowledgeOwl dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-knowledgeowl-from-the-gallery"></a>Adicionando o KnowledgeOwl da Galeria

Para configurar a integração do KnowledgeOwl ao Azure AD, você precisará adicionar o KnowledgeOwl da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o KnowledgeOwl da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **KnowledgeOwl**, selecione **KnowledgeOwl** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![KnowledgeOwl na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o KnowledgeOwl, com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado do KnowledgeOwl.

Para configurar e testar o logon único do Azure AD com o KnowledgeOwl, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do KnowledgeOwl](#configure-knowledgeowl-single-sign-on)** : para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do KnowledgeOwl](#create-knowledgeowl-test-user)** : para ter um equivalente de Brenda Fernandes no KnowledgeOwl que esteja vinculado à representação de usuário no Microsoft Azure Active Directory.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure Active Directory com o KnowledgeOwl, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **KnowledgeOwl**, clique em **Logon Único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único no Domínio e nas URLs do KnowledgeOwl](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    ||
    |-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    ||
    |-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único no Domínio e nas URLs do KnowledgeOwl](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:

    ||
    |-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Esses valores não são reais. Você precisará atualizar esse valor do Identificador, das URLs de resposta e da URL de entrada reais que são explicados posteriormente no tutorial.

6. O aplicativo KnowledgeOwl espera as instruções de declaração do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário**.

    ![image](common/edit-attribute.png)

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas: 

    | NOME | Atributo de Origem | Namespace |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Namespace**, digite o valor de namespace mostrado para essa linha.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Bruto)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

9. Na seção **Configurar o KnowledgeOwl**, copie a(s) URL(s) apropriada(s) de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-knowledgeowl-single-sign-on"></a>Configurar o logon único do KnowledgeOwl

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do KnowledgeOwl como administrador.

1. Clique em **Configurações** e, em seguida, selecione **Segurança**.

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Role até a **Integração de SSO do SAML** e execute as seguintes etapas:

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. Selecione **Habilitar SSO do SAML**.

    b. Copie o valor da **ID da Entidade SP** e cole-o na caixa de texto **Identificador (ID da Entidade)** na seção **Configuração Básica de SAML** no portal do Azure.

    c. Copie o valor da **URL de Logon SP** e cole-o nas caixas de texto **URL de Logon e URL de Resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    d. Na caixa de texto **ID da entidade de IdP**, cole o valor do **Identificador do Microsoft Azure Active Directory** copiado do portal do Azure.

    e. Na caixa de texto **URL de Logon de IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    f. Na caixa de texto **URL de Logon de IdP**, cole o valor da **URL de Logout** copiado do portal do Azure

    g. Carregue o formulário de certificado baixado do portal do Azure clicando em **Carregar certificado de IdP**.

    h. Clique em **Mapa de atributos SAML** para mapear atributos e executar as seguintes etapas:

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` na caixa de texto **ID do SSO**
    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` na caixa de texto **Email/Nome de usuário**.
    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` na caixa de texto **Nome**.
    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` na caixa de texto **Sobrenome**.
    * Clique em **Salvar**

    i. Na parte inferior da página, clique em **Salvar** .

    ![Configuração do KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao KnowledgeOwl.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os Aplicativos** e, em seguida, **KnowledgeOwl**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **KnowledgeOwl**.

    ![O link do KnowledgeOwl na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-knowledgeowl-test-user"></a>Criar um usuário de teste do KnowledgeOwl

Nesta seção, um usuário chamado Brenda Fernandes será criado no KnowledgeOwl. O KnowledgeOwl dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no KnowledgeOwl, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco KnowledgeOwl no Painel de Acesso, você deve ser conectado automaticamente ao KnowledgeOwl no qual configurou o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
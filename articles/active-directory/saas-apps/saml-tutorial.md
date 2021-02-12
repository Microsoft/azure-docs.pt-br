---
title: 'Tutorial: Integração do Active Directory do Azure com o aplicativo LOB habilitado para token SAML 1.1 | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Active Directory do Azure e o Aplicativo LOB habilitado para Token SAML 1.1.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 0b15d560e2678772cefdf3d87c047013b24ed467
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010271"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Tutorial: Integração do Active Directory do Azure com o Aplicativo LOB habilitado para Token SAML 1.1

Neste tutorial, você aprenderá a integrar o LOB App habilitado para Token de SAML 1.1 com o Azure AD (Azure Active Directory).
Integrando o SAML 1.1 O aplicativo LOB habilitado para token com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao LOB do SAML 1.1 habilitado para token.
* Você pode permitir que seus usuários façam logon automaticamente no Aplicativo LOB habilitado para Token SAML 1.1 (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD com o aplicativo LOB habilitado para Token SAML 1.1, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Aplicativo LOB habilitado para Token SAML 1.1 habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Aplicativo LOB habilitado para Token SAML 1.1 é compatível com o SSO iniciado por **SP**

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Adicionando LOB SAML 1.1 ativado por token App da galeria

Para configurar a integração do Aplicativo LOB habilitado para Token SAML 1.1 no Azure AD, você precisa adicionar o Aplicativo LOB habilitado para Token SAML 1.1 da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Aplicativo de LOB habilitado para Token SAML 1.1 da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **LOB App ativada por tokens do SAML 1.1**, selecione **App LOB do SAML 1.1 ativada por tokens** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

     ![SAML 1.1 Token habilitado App LOB na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Aplicativo LOB habilitado para Token SAML 1.1 com base em um usuário de teste chamado **Brenda Fernandes**.
Para o logon único funcionar, um relacionamento de vínculo entre um usuário do Azure AD e o usuário relacionado no Aplicativo LOB habilitado para Token SAML 1.1 precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o aplicativo LOB habilitado para Token SAML 1.1, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do aplicativo LOB habilitado para Token SAML 1.1](#configure-saml-11-token-enabled-lob-app-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Aplicativo LOB habilitado para Token SAML 1.1](#create-saml-11-token-enabled-lob-app-test-user)** – para ter um equivalente de Brenda Fernandes no Aplicativo LOB habilitado para Token SAML 1.1 vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Aplicativo LOB habilitado para Token SAML 1.1, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do **Aplicativo LOB habilitado para Token SAML 1.1**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![SAML 1.1 Domínio de aplicativo de LOB habilitado para token e informações de logon único de URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://your-app-url`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://your-app-url`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a equipe de suporte ao cliente do Aplicativo LOB habilitado para Token SAML 1.1 para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Aplicativo LOB habilitado para Token SAML 1.1**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-saml-11-token-enabled-lob-app-single-sign-on"></a>Configurar o logon único do Aplicativo LOB habilitado para Token SAML 1.1

Para configurar o logon único no lado do **Aplicativo LOB habilitado para Token SAML 1.1**, é necessário enviar as URLs copiadas apropriadas e o **Certificado (Base64)** que você baixou do portal do Azure para a equipe de suporte do Aplicativo LOB habilitado para Token SAML 1.1. Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Aplicativo LOB habilitado para Token SAML 1.1.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Aplicativo LOB habilitado para Token SAML 1.1**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Aplicativo LOB habilitado para Token SAML 1.1**.

    ![O link do Aplicativo de LOB habilitado para Token SAML 1.1 na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Criar um usuário de teste do Aplicativo LOB habilitado para Token SAML 1.1

Nesta seção, você cria um usuário chamado Brenda Fernandes no Aplicativo LOB habilitado para Token SAML 1.1. Trabalhe com a equipe de suporte do Aplicativo LOB habilitado para Token SAML 1.1 para adicionar os usuários na plataforma do Aplicativo LOB habilitado para Token SAML 1.1. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco do Aplicativo LOB habilitado para Token SAML 1.1 no Painel de Acesso, você deverá entrar automaticamente no Aplicativo LOB habilitado para Token SAML 1.1 que configurou para SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
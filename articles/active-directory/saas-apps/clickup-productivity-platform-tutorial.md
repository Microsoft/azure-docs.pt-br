---
title: 'Tutorial: Integração do Azure Active Directory com a ClickUp Productivity Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Software de Produtividade de ClickUp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: a6958e88e7e20b94a54216a92651c2f6d3fe650e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105308"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutorial: Integração do Azure Active Directory com a ClickUp Productivity Platform

Neste tutorial, você aprende a integrar a ClickUp Productivity Platform ao Azure AD (Azure Active Directory).
A integração da ClickUp Productivity Platform com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso à ClickUp Productivity Platform.
* Você pode permitir que os usuários sejam conectados automaticamente à ClickUp Productivity Platform (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a ClickUp Productivity Platform, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único da ClickUp Productivity Platform

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A ClickUp Productivity Platform dá suporte a SSO iniciado por **SP**

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Adicionar ClickUp Productivity Platform da galeria

Para configurar a integração da ClickUp Productivity Platform ao Azure AD, você precisará adicionar a ClickUp Productivity Platform da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a ClickUp Productivity Platform, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ClickUp Productivity Platform**, selecione **ClickUp Productivity Platform** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ClickUp Productivity Platform na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com a ClickUp Productivity Platform, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na ClickUp Productivity Platform.

Para configurar e testar o logon único do Azure AD com a ClickUp Productivity Platform, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único da ClickUp Productivity Platform](#configure-clickup-productivity-platform-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste da ClickUp Productivity Platform](#create-clickup-productivity-platform-test-user)** – para ter um equivalente de Brenda Fernandes na ClickUp Productivity Platform que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com a ClickUp Productivity Platform, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **ClickUp Productivity Platform**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon de único de logon único de URLs e Domínio da ClickUp Productivity Platform](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon**, digite uma URL: `https://app.clickup.com/login/sso`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize esse valor com o Identificador real, que é explicado posteriormente neste tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Configurar logon único da ClickUp Productivity Platform

1. Em uma janela de navegador da Web diferente, entre com seu locatário da Plataforma de Produtividade ClickUp como administrador.

2. Clique em **Perfil do usuário** e selecione **Configurações**.

    ![Configuração de produtividade do ClickUp](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Selecione **Microsoft** em um único Provedor de SSO (Logon Único).

    ![Configuração de produtividade do ClickUp](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Na página **Configurar Logon Único da Microsoft**, execute as seguintes etapas:

    ![Configuração de produtividade do ClickUp](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Clique em **Copiar** para copiar o valor de ID da entidade e cole-o na caixa de texto **Identificador (ID da Entidade)** na seção **Configuração Básica de SAML** no portal do Azure.
    
    b. Na caixa de texto **URL de Metadados de Federação do Azure**, cole o valor de URL de Metadados de Federação de Aplicativos que você copiou do portal do Azure e clique em **Salvar**.

5. Para concluir a instalação, clique em **Autenticar com a Microsoft para concluir a instalação** e autenticar com uma conta Microsoft.

    ![Configuração de produtividade do ClickUp](./media/clickup-productivity-platform-tutorial/configure4.png)

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a ela acesso à ClickUp Productivity Platform.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **ClickUp Productivity Platform**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ClickUp Productivity Platform**.

    ![O link da ClickUp Productivity Platform na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-clickup-productivity-platform-test-user"></a>Criar usuário de teste da ClickUp Productivity Platform

1. Em uma janela de navegador da Web diferente, entre com seu locatário da Plataforma de Produtividade ClickUp como administrador.

2. Clique em **Perfil do usuário** e selecione **Usuários**.

    ![Configuração de produtividade do ClickUp](./media/clickup-productivity-platform-tutorial/user1.png)

3. Insira o endereço de email do usuário na caixa de texto e clique em **Convidar**.

    ![Configuração de produtividade do ClickUp](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > O usuário receberá a notificação e precisará aceitar o convite para ativar a conta.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco da ClickUp Productivity Platform no Painel de Acesso, você deverá ser conectado automaticamente à ClickUp Productivity Platform para a qual você configura o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


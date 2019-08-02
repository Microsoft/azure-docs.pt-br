---
title: 'Tutorial: Integração do Azure Active Directory com o Uberflip | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: fb55840a3423f32d2d6d6d2531628ae4361a0cc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088203"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Tutorial: Integração do Azure Active Directory com o Uberflip

Neste tutorial, você aprenderá a integrar o Uberflip ao Azure AD (Azure Active Directory).

A integração do Uberflip com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Uberflip.
* Você pode permitir que seus usuários entrem automaticamente no Uberflip (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para obter detalhes sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Uberflip, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do Uberflip com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

O Uberflip dá suporte aos seguintes recursos:

* SSO (logon único) iniciado por SP e iniciado por IDP.
* Provisionamento Just-In-Time do usuário.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Adicionar o Uberflip do Azure Marketplace

Para configurar a integração do Uberflip ao Azure AD, você precisa adicionar o Uberflip do Azure Marketplace à sua lista de aplicativos SaaS gerenciados:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. No painel esquerdo, selecione **Azure Active Directory**.

   ![A opção Azure Active Directory](common/select-azuread.png)

1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.

   ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **+ Novo aplicativo** na parte superior do painel.

   ![A opção Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **Uberflip**. Nos resultados da pesquisa, selecione **Uberflip** e, em seguida, selecione **Adicionar** para adicionar o aplicativo.

   ![Uberflip na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Uberflip com base em uma usuária de teste chamada **B Fernandes**. Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e um usuário relacionado do Uberflip.

Para configurar e testar o logon único do Azure AD com o Uberflip, você precisa concluir os seguintes blocos de construção:

1. **[Configure o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para permitir que seus usuários usem esse recurso.
1. **[Configurar o logon único do Uberflip](#configure-uberflip-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
1. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Crie um usuário de teste do Uberflip](#create-an-uberflip-test-user)** para que haja, no Uberflip, um usuário chamado B. Fernandes que esteja vinculado ao usuário do Azure AD chamado B. Fernandes.
1. **[Teste o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Uberflip, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Uberflip**, selecione **Logon único**.

    ![Opção Configurar logon único](common/select-sso.png)

1. No painel **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. Na página **Configurar Logon Único com o SAML**, selecione **Editar** (o ícone de lápis) para abrir o painel **Configuração Básica do SAML**.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. No painel **Configuração básica do SAML**, siga uma das etapas a seguir, dependendo de qual modo de SSO você deseja configurar:

   * Para configurar o aplicativo no modo SSO iniciado por IDP, na caixa **URL de Resposta (URL do Serviço do Consumidor de Declaração)** , digite uma URL usando o seguinte padrão:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Informações de logon único de domínio e URLs do Uberflip](common/both-replyurl.png)

     > [!NOTE]
     > Esse valor não é real. Atualize esse valor com a URL de resposta real. Para obter o valor real, entre em contato com a [equipe de suporte do Uberflip](mailto:support@uberflip.com). Você também pode consultar os padrões exibidos no painel **Configuração Básica de SAML** no portal do Azure.

   * Para configurar o aplicativo no modo de SSO iniciado por SP, selecione **Definir URLs adicionais** e, na caixa **URL de logon**, digite esta URL:

     `https://app.uberflip.com/users/login`

     ![Informações de logon único de domínio e URLs do Uberflip](common/both-signonurl.png)

1. No painel **Configurar Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para baixar o **XML de Metadados de Federação** das opções fornecidas e salve-o em seu computador.

   ![A opção de download do XML de Metadados de Federação](common/metadataxml.png)

1. No painel **Configurar o Uberflip**, copie a URL ou URLs que você precisa:

   * **URL de logon**
   * **Identificador do Azure AD**
   * **URL de logoff**

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Configurar o logon único do Uberflip

Para configurar o logon único no lado do Uberflip, é necessário enviar o XML de Metadados de Federação baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Uberflip](mailto:support@uberflip.com). A equipe do Uberflip garantirá que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B. Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

    ![As opções "Usuários" e "Todos os usuários"](common/users.png)

1. Na parte superior da tela, selecione **+ Novo usuário**.

    ![Opção Novo usuário](common/new-user.png)

1. No painel **Usuário**, siga as etapas a seguir:

    ![O painel Usuário](common/user-properties.png)

    1. Na caixa **Nome**, insira **BFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **BFernandes\@\<domíniodaempresa>.\<extensão>** . Por exemplo, **BFernandes\@contoso.com**.

    1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo-lhe acesso ao Uberflip.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos** > **Uberflip**.

    ![Painel Aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Uberflip**.

    ![Uberflip na lista de aplicativos](common/all-applications.png)

1. No painel esquerdo, em **GERENCIAR**, selecione **Usuários e grupos**.

    ![A opção "Usuários e grupos"](common/users-groups-blade.png)

1. Selecione **+ Adicionar usuário** e, em seguida, selecione **Usuários e grupos** no painel **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

1. No painel **Usuários e grupos**, selecione **B. Fernandes** na lista **Usuários** e, em seguida, escolha **Selecionar** na parte inferior do painel.

1. Se você está esperando um valor de função na declaração SAML, no painel **Selecionar Função**, selecione na lista a função apropriada para o usuário. Escolha **Selecionar** na parte inferior do painel.

1. No painel **Adicionar atribuição**, selecione **Atribuir**.

### <a name="create-an-uberflip-test-user"></a>Criar um usuário de teste do Uberflip

Um usuário chamado B. Fernandes agora é criado no Uberflip. Você não precisa fazer nada para criar esse usuário. O Uberflip dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Se um usuário chamado B. Fernandes ainda não existir no Uberflip, um será criado após a autenticação.

> [!NOTE]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Quando você seleciona **Uberflip** no portal meus aplicativos, você deverá ser conectado automaticamente à assinatura do Uberflip para a qual você configura o logon único. Para obter mais informações sobre o portal Meus Aplicativos, veja [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

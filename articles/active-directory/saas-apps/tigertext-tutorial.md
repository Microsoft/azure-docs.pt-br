---
title: 'Tutorial: Integração do Azure Active Directory ao TigerText Secure Messenger | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088670"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Tutorial: Integração do Azure Active Directory ao TigerText Secure Messenger

Neste tutorial, você aprenderá a integrar o TigerText Secure Messenger ao Azure AD (Azure Active Directory).

A integração do TigerText Secure Messenger ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao TigerText Secure Messenger.
* Você pode permitir que os usuários sejam conectados automaticamente ao TigerText Secure Messenger (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para obter detalhes sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TigerText Secure Messenger, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do TigerText Secure Messenger habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o TigerText Secure Messenger ao Azure AD.

O TigerText Secure Messenger dá suporte ao SSO (logon único) iniciado por SP.

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Adicionar o TigerText Secure Messenger a partir do Azure Marketplace

Para configurar a integração do TigerText Secure Messenger ao Azure AD, você precisa adicionar o TigerText Secure Messenger a partir do Azure Marketplace à sua lista de aplicativos SaaS gerenciados:

1. Entre no [Portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Active Directory](common/select-azuread.png)

1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **+ Novo aplicativo** na parte superior do painel.

    ![A opção Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **TigerText Secure Messenger**. Nos resultados da pesquisa, escolha **TigerText Secure Messenger** e, em seguida, escolha **Adicionar** para adicionar o aplicativo.

    ![TigerText Secure Messenger na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o TigerText Secure Messenger, com base em um usuário de teste chamado **Brenda Fernandes**. Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TigerText Secure Messenger.

Para configurar e testar o logon único do Azure AD com o TigerText Secure Messenger, você precisará concluir os seguintes blocos de construção:

1. **[Configure o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para permitir que seus usuários usem esse recurso.
1. **[Configurar o logon único do TigerText Secure Messenger](#configure-tigertext-secure-messenger-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
1. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Criar um usuário de teste do TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)** – para ter o usuário Brenda Fernandes no TigerText Secure Messenger vinculado à representação do usuário Brenda Fernandes no Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TigerText Secure Messenger, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TigerText Secure Messenger**, selecione **Logon único**.

    ![Opção Configurar logon único](common/select-sso.png)

1. No painel **Escolher um método de logon único**, escolha o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. Na página **Configurar Logon Único com o SAML**, selecione **Editar** (o ícone de lápis) para abrir o painel **Configuração Básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. No painel **Configuração Básica de SAML**, execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do TigerText Secure Messenger](common/sp-identifier.png)

    1. Na caixa **URL de Logon**, insira uma URL:

       `https://home.tigertext.com`

    1. Na caixa **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > O valor **Identificador (ID da entidade)** não é real. Atualize esse valor com o Identificador real. Para obter o valor, contate a [equipe de suporte do TigerText Secure Messenger](mailto:prosupport@tigertext.com). Você também pode consultar os padrões exibidos no painel **Configuração Básica de SAML** no portal do Azure.

1. No painel **Configurar Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para baixar o **XML de Metadados de Federação** das opções fornecidas e salve-o em seu computador.

    ![A opção de download do XML de Metadados de Federação](common/metadataxml.png)

1. Na seção **Configurar o TigerText Secure Messenger**, copie a URL ou URLs necessárias:

   * **URL de logon**
   * **Identificador do Azure AD**
   * **URL de logoff**

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Configurar o logon único do TigerText Secure Messenger

Para configurar o logon único no lado do TigerText Secure Messenger, é necessário enviar o XML de Metadados de Federação baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do TigerText Secure Messenger](mailto:prosupport@tigertext.com). A equipe do TigerText Secure Messenger garantirá que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada Brenda Fernandes.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**   > **Usuários** > **Todos os usuários**.

    ![As opções "Usuários" e "Todos os usuários"](common/users.png)

1. Na parte superior da tela, selecione **+ Novo usuário**.

    ![Opção Novo usuário](common/new-user.png)

1. No painel **Usuário**, siga as etapas a seguir:

    ![O painel Usuário](common/user-properties.png)

    1. Na caixa **Nome**, insira **BrendaFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **BrendaFernandes\@\<domíniodaempresa>.\<extensão>** . Por exemplo, **BrendaFernandes\@contoso.com**.

    1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TigerText Secure Messenger.

1. No portal do Azure, escolha **Aplicativos Empresariais** > **Todos os aplicativos** > **TigerText Secure Messenger**.

    ![Painel Aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **TigerText Secure Messenger**.

    ![O TigerText Secure Messenger na lista de aplicativos](common/all-applications.png)

1. No painel esquerdo, em **GERENCIAR**, selecione **Usuários e grupos**.

    ![A opção "Usuários e grupos"](common/users-groups-blade.png)

1. Selecione **+ Adicionar usuário** e, em seguida, selecione **Usuários e grupos** no painel **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

1. No painel **Usuários e grupos**, selecione **Brenda Fernandes** na lista **Usuários** e, em seguida, escolha **Selecionar** na parte inferior do painel.

1. Se você está esperando um valor de função na declaração SAML, no painel **Selecionar Função**, selecione na lista a função apropriada para o usuário. Escolha **Selecionar** na parte inferior do painel.

1. No painel **Adicionar atribuição**, selecione **Atribuir**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Criar um usuário de teste do TigerText Secure Messenger

Nesta seção, você criará um usuário chamado Brenda Fernandes no TigerText Secure Messenger. Trabalhe com a [equipe de suporte do TigerText Secure Messenger](mailto:prosupport@tigertext.com) para adicionar Brenda Fernandes como usuário do TigerText Secure Messenger. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Quando escolher **TigerText Secure Messenger** no portal Meus Aplicativos, você deverá ser conectado automaticamente à assinatura do TigerText Secure Messenger para a qual você configurou o logon único. Para obter mais informações sobre o portal Meus Aplicativos, veja [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais de integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

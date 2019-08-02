---
title: 'Tutorial: Integração do Azure Active Directory com o PageDNA | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227475"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Tutorial: Integração do Azure Active Directory com o PageDNA

Neste tutorial, você aprenderá a integrar o PageDNA ao Azure AD (Azure Active Directory).

A integração do PageDNA ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao PageDNA.
* Você pode permitir que os usuários sejam conectados automaticamente ao PageDNA (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para obter detalhes sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao PageDNA, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do PageDNA com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o PageDNA ao Azure AD.

O PageDNA dá suporte aos seguintes recursos:

* SSO (logon único) iniciado por SP.

* Provisionamento Just-In-Time do usuário.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Adicionar o PageDNA por meio do Azure Marketplace

Para configurar a integração do PageDNA ao Azure AD, você precisará adicionar o PageDNA por meio do Azure Marketplace à lista de aplicativos SaaS gerenciados:

1. Entre no [Portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Active Directory](common/select-azuread.png)

1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **+ Novo aplicativo** na parte superior do painel.

    ![A opção Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **PageDNA**. Nos resultados da pesquisa, selecione **PageDNA** e, em seguida, **Adicionar** para adicionar o aplicativo.

    ![PageDNA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o PageDNA, com base em um usuário de teste chamado **Brenda Fernandes**. Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PageDNA.

Para configurar e testar o logon único do Azure AD com o PageDNA, você precisa concluir os seguintes blocos de construção:

1. **[Configure o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para permitir que seus usuários usem esse recurso.
1. **[Configurar o logon único do PageDNA](#configure-pagedna-single-sign-on)** para definir as configurações de logon único no lado do aplicativo.
1. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Criar um usuário de teste do PageDNA](#create-a-pagedna-test-user)** , para que haja um usuário chamado Brenda Fernandes no PageDNA que esteja vinculado ao usuário do Azure AD chamado Brenda Fernandes.
1. **[Teste o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o PageDNA, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **PageDNA**, selecione **Logon único**.

    ![Opção Configurar logon único](common/select-sso.png)

1. No painel **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. Na página **Configurar Logon Único com o SAML**, selecione **Editar** (o ícone de lápis) para abrir o painel **Configuração Básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. No painel **Configuração Básica do SAML**, conclua as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do PageDNA](common/sp-identifier.png)

    1. Na caixa **URL de Logon**, insira uma URL usando um dos seguintes padrões:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. Na caixa **Identificador (ID da Entidade)** , insira uma URL usando um dos seguintes padrões:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Para obter esses valores, contate a [equipe de suporte do PageDNA](mailto:success@pagedna.com). Você também pode consultar os padrões exibidos no painel **Configuração Básica de SAML** no portal do Azure.

1. No painel **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para baixar o **Certificado (Bruto)** com base nas opções fornecidas e salve-o no computador.

    ![A opção de download Certificado (Bruto)](common/certificateraw.png)

1. Na seção **Configurar o PageDNA**, copie as URLs necessárias:

   * **URL de logon**
   * **Identificador do Azure AD**
   * **URL de logoff**

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Configurar o logon único do PageDNA

Para configurar o logon único no lado do PageDNA, envie o Certificado (Bruto) baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do PageDNA](mailto:success@pagedna.com). A equipe do PageDNA garantirá que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

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

Nesta seção, ao conceder acesso ao PageDNA ao usuário, você permitirá que o usuário Brenda Fernandes use o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos empresariais** > **Todos os aplicativos** > **PageDNA**.

    ![Painel Aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, escolha **PageDNA**.

    ![PageDNA na lista de aplicativos](common/all-applications.png)

1. No painel esquerdo, em **GERENCIAR**, selecione **Usuários e grupos**.

    ![A opção "Usuários e grupos"](common/users-groups-blade.png)

1. Selecione **+ Adicionar usuário** e, em seguida, selecione **Usuários e grupos** no painel **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

1. No painel **Usuários e grupos**, selecione **Brenda Fernandes** na lista **Usuários** e, em seguida, escolha **Selecionar** na parte inferior do painel.

1. Se você está esperando um valor de função na declaração SAML, no painel **Selecionar Função**, selecione na lista a função apropriada para o usuário. Escolha **Selecionar** na parte inferior do painel.

1. No painel **Adicionar atribuição**, selecione **Atribuir**.

### <a name="create-a-pagedna-test-user"></a>Criar um usuário de teste do PageDNA

Um usuário chamado Brenda Fernandes agora foi criado no PageDNA. Você não precisa fazer nada para criar esse usuário. O PageDNA dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Se um usuário chamado Brenda Fernandes ainda não existir no PageDNA, um será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Ao selecionar o **PageDNA** no portal Meus Aplicativos, você deverá ser conectado automaticamente à assinatura do PageDNA, para a qual você configurou o logon único. Para obter mais informações sobre o portal Meus Aplicativos, veja [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


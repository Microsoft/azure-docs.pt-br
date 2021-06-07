---
title: 'Tutorial: Integração do Azure Active Directory ao Mind Tools Toolkit | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mind Tools Toolkit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 57598ea22874cfba772f6cf0e6b2ba779ef7c482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92448343"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Tutorial: Integração do Azure Active Directory ao Mind Tools Toolkit

Neste tutorial, você aprende a integrar o Mind Tools Toolkit ao Azure AD (Azure Active Directory).

Com essa integração, você pode:

* Controlar no Azure AD quem tem acesso ao Mind Tools Toolkit.
* Permitir que seus usuários sejam conectados automaticamente ao Mind Tools Toolkit (logon único) com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Mind Tools Toolkit, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Mind Tools Toolkit com o SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Mind Tools Toolkit é compatível com o SSO iniciado por SP.
* O Mind Tools Toolkit é compatível com o provisionamento de usuário Just-In-Time.
* Depois de configurar o Mind Tools Toolkit, você poderá impor o controle de sessão. Esse controle protege contra a exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Adicionar o Mind Tools Toolkit da galeria

Para configurar a integração do Mind Tools Toolkit ao Azure AD, é necessário adicionar o Mind Tools Toolkit à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel de navegação mais esquerdo, escolha o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, insira **Mind Tools Toolkit** na caixa de pesquisa.
1. Selecione **Mind Tools Toolkit** nos resultados da pesquisa e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Mind Tools Toolkit, usando uma usuária de teste chamada **B.Fernandes**. Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Mind Tools Toolkit.

Para configurar e testar o logon único do Azure AD com o Mind Tools Toolkit, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Mind Tools Toolkit](#configure-mind-tools-toolkit-sso)** para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Mind Tools Toolkit](#create-a-mind-tools-toolkit-test-user)** para ter um equivalente de B.Fernandes no Mind Tools Toolkit. Esse equivalente é vinculado à representação do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você configurará o logon único do Azure AD com o Mind Tools Toolkit seguindo estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **Mind Tools Toolkit**, selecione **Logon único**.

    ![A seção Gerenciar, com logon único realçado](common/select-sso.png)

1. Na caixa de diálogo **Selecionar um Método de logon único**, selecione **SAML/WS-Fed** para habilitar o logon único.

    ![A caixa de diálogo Selecione um método de logon único, com SAML realçado](common/select-saml-option.png)

1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

    ![A página Configurar Logon Único com SAML, com o ícone de lápis para Configuração Básica de SAML realçado](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, na caixa **URL de Logon**, insira uma URL que tenha o padrão `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>`.

    > [!NOTE]
    > O valor **URL de Logon** não é real. Atualize o valor com a URL de logon real. Entre em contato com a [equipe de suporte ao cliente do Mind Tools Toolkit](mailto:support@goodpractice.com) para obter o valor.

1. Na página **Configurar Logon Único com SAML**, acesse a seção **Certificado de Autenticação SAML**. À direita de **XML de Metadados de Federação**, selecione **Baixar** para baixar o texto XML e salvá-lo no computador. O conteúdo do XML dependerá das opções que você selecionar.

    ![A seção Certificado de Autenticação SAML, com Baixar realçado ao lado do XML de Metadados de Federação](common/metadataxml.png)

1. Na seção **Configurar o Mind Tools Toolkit**, copie o que for necessário para as URLs a seguir.

    * **URL de logon**

    * **Identificador do Azure AD**

    * **URL de logoff**

    ![A seção Configurar o Mind Tools Toolkit, com as URLs de configuração realçadas](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B.Fernandes:

1. No lado mais à esquerda do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Na parte superior da tela, selecione **Novo usuário**.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira **B.Fernandes**.  
   1. No campo **Nome de usuário**, insira **B.Fernandes@** _domíniodaempresa_ **.** _extensão_. Por exemplo, B.Simon@contoso.com.
   1. Marque a caixa de seleção **Mostrar senha**, em seguida, anote o valor mostrado na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o B.Fernandes use o logon único do Azure, permitindo acesso ao Mind Tools Toolkit.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Mind Tools Toolkit**.
1. Na página de visão geral do aplicativo, acesse a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![A seção Gerenciar, com Usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

   ![A janela Usuários e grupos, com Adicionar usuário realçado](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Se você esperar qualquer valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-mind-tools-toolkit-sso"></a>Configurar o SSO do Mind Tools Toolkit

Para configurar o logon único no **Mind Tools Toolkit**, envie o texto do **XML de Metadados de Federação** baixado e as URLs copiadas anteriormente para a [equipe de suporte do Mind Tools Toolkit](mailto:support@goodpractice.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-a-mind-tools-toolkit-test-user"></a>Criar um usuário de teste do Mind Tools Toolkit

Nesta seção, uma usuária chamada B.Fernandes será criada no Mind Tools Toolkit.

O Mind Tools Toolkit é compatível com o provisionamento Just-In-Time, que está habilitado por padrão. Não há nenhuma ação para você executar nesta seção. Se um usuário ainda não existir no Mind Tools Toolkit, um novo será criado quando você tentar acessar o Mind Tools Toolkit.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Ao selecionar o bloco do Mind Tools Toolkit no portal Meus Aplicativos, você será conectado automaticamente ao Mind Tools Toolkit para o qual configurou o SSO. Para obter mais informações sobre o portal Meus Aplicativos, confira [Introdução ao portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais de integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Mind Tools Toolkit com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Mind Tools Toolkit com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
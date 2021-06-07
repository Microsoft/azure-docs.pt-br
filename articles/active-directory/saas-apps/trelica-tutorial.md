---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Trelica | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Trelica.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: a674f5f653ad420ab8f28ff73c6b86f9c18b154e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92517745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Trelica

Neste tutorial, você aprenderá a integrar o Trelica ao Azure AD (Azure Active Directory).

Com essa integração, você pode:

* Controlar no Azure AD quem tem acesso ao Trelica.
* Permitir que os usuários sejam conectados automaticamente ao Trelica com as próprias contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Trelica com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Trelica é compatível com o SSO iniciado por IDP.
* O Trelica é compatível com o provisionamento de usuário Just-In-Time.
* Depois de configurar o Trelica, você poderá impor o controle de sessão. Esse controle protege contra a exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Adicionar o Trelica da galeria

Para configurar a integração do Trelica ao Azure AD, você precisará adicionar o Trelica da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel de navegação mais esquerdo, escolha o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, insira **Trelica** na caixa de pesquisa.
1. Selecione **Trelica** nos resultados da pesquisa e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Configurar e testar logon único do Azure AD para o Trelica

Configure e teste o SSO do Azure AD com o Trelica usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Trelica.

Para configurar e testar o SSO do Azure AD com o Trelica, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Trelica](#configure-trelica-sso)** para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Trelica](#create-a-trelica-test-user)** para ter um equivalente de B.Fernandes no Trelica. Esse equivalente é vinculado à representação do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Trelica**, acesse a seção **Gerenciar**. Selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![A página Configurar Logon Único com SAML, com o ícone de lápis para Configuração Básica de SAML realçado](common/edit-urls.png)

1. Na página **Configurar Logon Único com SAML**, insira os seguintes valores:

    1. Na caixa **Identificador**, insira a URL **https://app.trelica.com** .

    1. Na caixa **URL de Resposta**, digite uma URL que tenha o padrão `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs`.

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize esse valor com a URL de Resposta real (também conhecida como o ACS).
    > Encontre isso fazendo logon no Trelica e acessando a [página de configuração provedores de identidade SAML](https://app.trelica.com/Admin/Profile/SAML) (Administrador > Conta > SAML). Clique no botão Copiar ao lado da **URL do ACS (serviço do consumidor de declaração)** para colocá-lo na área de transferência, pronto para colar na caixa de texto **URL de Resposta** no Azure AD.
    > Leia a [documentação de ajuda do Trelica](https://docs.trelica.com/admin/saml/azure-ad) ou entre em contato com a [equipe de suporte ao cliente do Trelica](mailto:support@trelica.com) em caso de dúvidas.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![A seção Certificado de Autenticação SAML, com o botão copiar realçado ao lado da URL de Metadados de Federação do Aplicativo](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B.Fernandes.

1. No painel mais à esquerda do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Na parte superior da tela, selecione **Novo usuário**.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira **B.Fernandes**.
   1. No campo **Nome de usuário**, insira **B.Fernandes@** _domíniodaempresa_ **.** _extensão_. Por exemplo, B.Simon@contoso.com.
   1. Marque a caixa de seleção **Mostrar senha**, em seguida, anote o valor mostrado na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Trelica.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Trelica**.
1. Na página de visão geral do aplicativo, acesse a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![A seção Gerenciar, com Usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

   ![A janela Usuários e grupos, com Adicionar usuário realçado](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Se você esperar qualquer valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-trelica-sso"></a>Configurar o SSO do Trelica

Para configurar o logon único no lado do **Trelica**, acesse a [página de configuração de provedores de identidade SAML](https://app.trelica.com/Admin/Profile/SAML) (Administrador > Conta > SAML). Clique no botão **Novo**. Insira **Azure AD** como o Nome e escolha **Metadados da URL** como o tipo de metadados. Cole a **URL de Metadados de Federação do Aplicativo** copiada do Azure AD no campo **URL de Metadados** do Trelica.

Leia a [documentação de ajuda do Trelica](https://docs.trelica.com/admin/saml/azure-ad) ou entre em contato com a [equipe de suporte ao cliente do Trelica](mailto:support@trelica.com) em caso de dúvidas.

### <a name="create-a-trelica-test-user"></a>Criar um usuário de teste do Trelica

O Trelica é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há nenhuma ação para você executar nesta seção. Se um usuário ainda não existir no Trelica, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Quando selecionar o bloco do Trelica no portal Meus Aplicativos, você será conectado automaticamente ao Trelica para o qual você configurou o SSO. Para obter mais informações sobre o portal Meus Aplicativos, confira [Introdução ao portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais de integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Trelica com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Trelica com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
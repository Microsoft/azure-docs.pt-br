---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao BrowserStack Single Sign-On | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BrowserStack Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: 49a66c265e5732c7a900a149af9f69b37efd5a5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456629"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-browserstack-single-sign-on"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao BrowserStack Single Sign-On

Neste tutorial, você aprenderá a integrar o BrowserStack Single Sign-On ao Azure AD (Azure Active Directory). Ao integrar o BrowserStack Single Sign-On ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao BrowserStack Single Sign-On.
* Habilitar seus usuários a serem conectados automaticamente ao BrowserStack Single Sign-On com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do BrowserStack Single Sign-On.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O BrowserStack Single Sign-On é compatível com SSO iniciado por **SP e IDP**

* Depois de configurar o BrowserStack Single Sign-On, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-browserstack-single-sign-on-from-the-gallery"></a>Como adicionar o BrowserStack Single Sign-On da galeria

Para configurar a integração do BrowserStack Single Sign-On ao Azure AD, você precisará adicionar o BrowserStack Single Sign-On da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **BrowserStack Single Sign-On** na caixa de pesquisa.
1. Selecione **BrowserStack Single Sign-On** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-browserstack-single-sign-on"></a>Configurar e testar o logon único do Azure AD para o BrowserStack Single Sign-On

Configure e teste o SSO do Azure AD com o BrowserStack Single Sign-On usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no BrowserStack Single Sign-On.

Para configurar e testar o SSO do Azure AD com o BrowserStack Single Sign-On, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do BrowserStack Single Sign-On](#configure-browserstack-single-sign-on-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do BrowserStack Single Sign-On](#create-browserstack-single-sign-on-test-user)** : para ter um equivalente de B.Fernandes no BrowserStack Single Sign-On que esteja vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **BrowserStack Single Sign-On**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://login.browserstack.com/auth/realms/<REALM_ID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.browserstack.com/auth/realms/<REALM_ID>/broker/<BROKER_ID>/endpoint`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://browserstack.com/users/sign_in`

    > [!Note]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do BrowserStack Single Sign-On](mailto:support@browserstack.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Clique em **Save** (Salvar).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o BrowserStack Single Sign-On**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao BrowserStack Single Sign-On.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **BrowserStack Single Sign-On**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-browserstack-single-sign-on-sso"></a>Configurar o SSO do BrowserStack Single Sign-On

Para configurar o logon único no lado do **BrowserStack Single Sign-On**, você precisa enviar o **XML de metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do BrowserStack Single Sign-On](mailto:support@browserstack.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-browserstack-single-sign-on-test-user"></a>Criar usuário de teste do BrowserStack Single Sign-On

Nesta seção, você criará uma usuária chamada B.Fernandes no BrowserStack Single Sign-On. Trabalhe com a [equipe de suporte do BrowserStack Single Sign-On](mailto:support@browserstack.com) para adicionar os usuários na plataforma do BrowserStack Single Sign-On. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do BrowserStack Single Sign-On no Painel de Acesso, você deverá entrar automaticamente no BrowserStack Single Sign-On para o qual tiver configurado o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o BrowserStack Single Sign-On com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o BrowserStack Single Sign-On com controles e visibilidade avançados](/cloud-app-security/proxy-intro-aad)
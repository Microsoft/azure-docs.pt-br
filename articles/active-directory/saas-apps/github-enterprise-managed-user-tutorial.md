---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao GitHub Enterprise Managed User | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GitHub Enterprise Managed User.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2021
ms.author: jeedes
ms.openlocfilehash: 864415f421f4fbecf31fd52a624ac568b4cf9c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574757"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-managed-user"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao GitHub Enterprise Managed User

Neste tutorial, você aprenderá a integrar o GitHub Enterprise Managed User ao Azure AD (Azure Active Directory). Ao integrar o GitHub Enterprise Managed User ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao GitHub Enterprise Managed User.
* Permitir que os usuários entrem automaticamente no GitHub Enterprise Managed User com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do GitHub Enterprise Managed User habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O GitHub Enterprise Managed User dá suporte ao SSO iniciado por **SP e IdP**.
* O GitHub Enterprise Managed User dá suporte ao provisionamento de usuário **just-in-time**.
* O GitHub Enterprise Managed User dá suporte ao [provisionamento **automatizado** de usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/github-enterprise-managed-user-provisioning-tutorial).

## <a name="adding-github-enterprise-managed-user-from-the-gallery"></a>Como adicionar o GitHub Enterprise Managed User por meio da galeria

Para configurar a integração do GitHub Enterprise Managed User ao Azure AD, você precisará adicionar o GitHub Enterprise Managed User por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **GitHub Enterprise Managed User** na caixa de pesquisa.
1. Selecione **GitHub Enterprise Managed User** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-managed-user"></a>Configurar e testar o SSO do Azure AD para o GitHub Enterprise Managed User

Configure e teste o SSO do Azure AD com o GitHub Enterprise Managed User por meio de um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no GitHub Enterprise Managed User.

Para configurar e testar o SSO do Azure AD com o GitHub Enterprise Managed User, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do GitHub Enterprise Managed User](#configure-github-enterprise-managed-user-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do GitHub Enterprise Managed User](#create-github-enterprise-managed-user-test-user)** : para ter um equivalente de B.Fernandes no GitHub Enterprise Managed User que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **GitHub Enterprise Managed User**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://github.com/enterprise-managed/<ENTITY>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://github.com/enterprises/<ENTITY>/saml/consume`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://github.com/enterprises/<ENTITY>/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do GitHub Enterprise Managed User](mailto:support@github.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o GitHub Enterprise Managed User**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao GitHub Enterprise Managed User.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **GitHub Enterprise Managed User**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-github-enterprise-managed-user-sso"></a>Configurar o SSO do GitHub Enterprise Managed User

Para configurar o logon único no lado do **GitHub Enterprise Managed User**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do GitHub Enterprise Managed User](mailto:support@github.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-github-enterprise-managed-user-test-user"></a>Criar um usuário de teste do GitHub Enterprise Managed User

Nesta seção, um usuário chamado B.Fernandes será criado no GitHub Enterprise Managed User. O GitHub Enterprise Managed User dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no GitHub Enterprise Managed User, ele será criado quando você tentar acessar o GitHub Enterprise Managed User.

O GitHub Enterprise Managed User também dá suporte ao provisionamento automático de usuário. Encontre [aqui](https://docs.microsoft.com/azure/active-directory/saas-apps/github-enterprise-managed-user-provisioning-tutorial) mais detalhes sobre como configurá-lo.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do GitHub Enterprise Managed User, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do GitHub Enterprise Managed User e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no GitHub Enterprise Managed User, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do GitHub Enterprise Managed User em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no GitHub Enterprise Managed User, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o GitHub Enterprise Managed User, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).



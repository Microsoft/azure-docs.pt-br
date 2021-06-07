---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AwareGo | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AwareGo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 4682396f68d6ff1af0b2fb6a5b1a8419d6963529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735323"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Tutorial: Integração do logon único do Azure Active Directory ao AwareGo

Neste tutorial, você aprenderá a integrar o AwareGo ao Azure AD (Azure Active Directory). Com a integração do AwareGo ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao AwareGo.
* Permitir que os usuários sejam conectados automaticamente ao AwareGo com as respectivas contas do Azure AD.
* gerenciar suas contas em um local central, o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do AwareGo habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O AwareGo dá suporte ao SSO iniciado por um SP (provedor de serviços).


## <a name="adding-awarego-from-the-gallery"></a>Adicionar o AwareGo por meio da galeria

Para configurar a integração do AwareGo ao Azure AD, você precisará adicioná-lo por meio da galeria à lista de aplicativos SaaS (software como serviço) gerenciados.

1. Entre no portal do Azure usando uma conta corporativa, uma conta de estudante ou uma conta pessoal Microsoft.
1. No painel esquerdo, selecione o serviço **Microsoft Azure Active Directory**.
1. Selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AwareGo** na caixa de pesquisa.
1. No painel de resultados, selecione **AwareGo** e adicione o aplicativo. Em alguns segundos, o aplicativo será adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Configurar e testar o SSO do Azure AD para o AwareGo

Configure e teste o SSO do Azure AD com o AwareGo usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do AwareGo.

Para configurar e testar o SSO do Azure AD com o AwareGo, faça o seguinte:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.  

    a. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com o usuário B.Fernandes.  
    b. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que o usuário B.Fernandes use o logon único do Azure AD.  

1. **[Configurar o SSO do AwareGo](#configure-awarego-sso)** : para definir as configurações de logon único no lado do aplicativo.

    a. **[Criar um usuário de teste do AwareGo](#create-an-awarego-test-user)** : para ter um equivalente de B.Fernandes no AwareGo que esteja vinculado à representação de usuário do Azure AD.  
    b. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure, faça o seguinte:

1. No portal do Azure, na página de integração de aplicativos do **AwareGo**, em **Gerenciar**, selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Para editar as configurações, no painel **Configurar o Logon Único com o SAML**, selecione o botão **Editar**.

   ![Captura de tela do botão Editar de Configuração Básica do SAML.](common/edit-urls.png)

1. No painel Editar, em **Configuração Básica do SAML**, faça o seguinte:

    a. Na caixa **URL de Logon**, insira uma das seguintes URLs:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. Na caixa **Identificador (ID da Entidade)** , insira uma URL no seguinte formato: `https://<SUBDOMAIN>.awarego.com`

    c. Na caixa **URL de Resposta**, insira uma URL no seguinte formato: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > Os valores anteriores não são reais. Atualize-os com o identificador e as URLs de resposta reais. Para obter os valores, entre em contato com a [equipe de suporte ao cliente do AwareGo](mailto:support@awarego.com). Veja também os exemplos da seção **Configuração Básica do SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, ao lado de **Certificado (Base64)** , selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![Captura de tela do link "Baixar" do certificado no painel Certificado de Autenticação SAML.](common/certificatebase64.png)

1. Na seção **Configurar o AwareGo**, copie uma ou mais URLs, dependendo dos seus requisitos.

    ![Captura de tela do painel "Configurar o AwareGo" para copiar as URLs de configuração.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo no portal do Azure, selecione **Azure Active Directory** e **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. No painel de propriedades do **Usuário**, faça o seguinte:

   a. Na caixa **Nome**, insira **B.Fernandes**.  
   b. Na caixa **Nome de usuário**, insira o nome de usuários no seguinte formato: `<username>@<companydomain>.<extension>` (por exemplo, B.Simon@contoso.com).  
   c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa **Senha** para uso posterior.  
   d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o usuário B.Fernandes use o SSO do Azure permitindo acesso ao AwareGo.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista **Aplicativos**, selecione **AwareGo**.
1. Na página de visão geral do aplicativo, na seção **Gerenciar**, selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. No painel **Usuários e grupos**, na lista **Usuários**, selecione **B.Fernandes** e escolha o botão **Selecionar**.
1. Se estiver esperando atribuir uma função aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, a função *Acesso Padrão* será selecionada.
1. No painel **Adicionar Atribuição**, selecione o botão **Atribuir**.

## <a name="configure-awarego-sso"></a>Configurar o SSO do AwareGo

Para configurar o logon único no lado do **AwareGo**, envie o **Certificado (Base64)** baixado anteriormente e as URLs copiadas do portal do Azure para a [equipe de suporte do AwareGo](mailto:support@awarego.com). A equipe de suporte criará essa configuração para estabelecer a conexão de SSO do SAML corretamente em ambos os lados.

### <a name="create-an-awarego-test-user"></a>Criar um usuário de teste do AwareGo

Nesta seção, você criará um usuário chamado Brenda Fernandes no AwareGo. Trabalhe com a [equipe de suporte do AwareGo](mailto:support@awarego.com) para adicionar os usuários à plataforma AwareGo. Você precisará criar e ativar os usuários para usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você poderá testar sua configuração de logon único do Azure AD executando uma das seguintes ações: 

* No portal do Azure, selecione **Testar este aplicativo**. Isso redirecionará você para a página de entrada do AwareGo, na qual poderá iniciar o fluxo de entrada. 

* Acesse diretamente a página de entrada do AwareGo e inicie o fluxo de entrada nela.

* Acesse Meus Aplicativos da Microsoft. Ao selecionar o bloco do **AwareGo** em Meus Aplicativos, você será redirecionado para a página de entrada do AwareGo. Para obter mais informações, confira [Entrar e iniciar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o AwareGo, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle de sessão é estendido do Controle de Aplicativos de Acesso Condicional. Para obter mais informações, confira [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
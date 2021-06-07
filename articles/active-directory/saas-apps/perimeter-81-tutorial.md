---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Perimeter 81 | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Perimeter 81.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: jeedes
ms.openlocfilehash: cd6ba1da92a19a1f73fc67c0165bfb19b3bb77aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363773"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeter-81"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Perimeter 81

Neste tutorial, você aprenderá a integrar o Perimeter 81 ao Azure AD (Azure Active Directory). Ao integrar o Perimeter 81 ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Perimeter 81.
* Permitir que os usuários sejam conectados automaticamente ao Perimeter 81 com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Perimeter 81 habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Perimeter 81 é compatível com o SSO iniciado por **SP e IdP**
* O Perimeter 81 é compatível com o provisionamento de usuário **Just-in-Time**

## <a name="adding-perimeter-81-from-the-gallery"></a>Adicionar o Perimeter 81 da galeria

Para configurar a integração do Perimeter 81 ao Azure AD, é necessário adicionar o Perimeter 81 da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Perimeter 81** na caixa de pesquisa.
1. Selecione **Perimeter 81** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-perimeter-81"></a>Configurar e testar o SSO do Azure AD para o Perimeter 81

Configure e teste o SSO do Azure AD com o Perimeter 81 usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Perimeter 81.

Para configurar e testar o SSO do Azure AD com o Perimeter 81, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Perimeter 81](#configure-perimeter-81-sso)** : para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Perimeter 81](#create-perimeter-81-test-user)** : para ter um equivalente de B.Fernandes no Perimeter 81 que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Perimeter 81**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite um valor usando o seguinte padrão: `urn:auth0:perimeter81:<SUBDOMAIN>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://auth.perimeter81.com/login/callback?connection=<SUBDOMAIN>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.perimeter81.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do ao cliente do Perimeter 81](mailto:support@perimeter81.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Perimeter 81**, copie as URLs apropriadas de acordo com os seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Perimeter 81.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Perimeter 81**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-perimeter-81-sso"></a>Configurar o SSO do Perimeter 81

1. Para automatizar a configuração no Perimeter 81, é necessário instalar a **Extensão do navegador de Conexão Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, clicar em **Configurar o Perimeter 81** direcionará você ao aplicativo Perimeter 81. De lá, forneça as credenciais de administrador para entrar no Perimeter 81. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Perimeter 81 manualmente, em outra janela do navegador da Web, entre no site da empresa do Perimeter 81 como administrador.

4. Acesse **Configurações** e clique em **Provedores de Identidade**.

    ![Configurações do Perimeter 81](./media/perimeter-81-tutorial/settings.png)

5. Clique no botão **Adicionar provedor**.

    ![Provedor de adição do Perimeter 81](./media/perimeter-81-tutorial/add-provider.png)

6. Selecione **Provedores de Identidade SAML 2.0** e clique no botão **Continuar**.

    ![Provedor de identidade de adição do Perimeter 81](./media/perimeter-81-tutorial/add-identity-provider.png)

7. Na seção **Provedores de Identidade do SAML 2.0**, execute as seguintes etapas:

    ![SAML de configuração do Perimeter 81](./media/perimeter-81-tutorial/setting-up-saml.png)

    a. Na caixa de texto **URL de Entrada**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Na caixa de texto **Aliases de Domínio**, insira o seu valor de alias de domínio.

    c. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado de Autenticação X509**.

    > [!NOTE]
    > Como alternativa, você pode clicar em **Carregar o Arquivo PEM/CERT** para carregar o **Certificado (Base64)** que você baixou de portal do Azure.
    
    d. Clique em **Concluído**.

### <a name="create-perimeter-81-test-user"></a>Criar um usuário de teste do Perimeter 81

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Perimeter 81. O Perimeter 81 é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Perimeter 81, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Perimeter 81, em que você pode iniciar o fluxo de logon.  

* Acesse a URL de Logon do Perimeter 81 diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Perimeter 81 para o qual o SSO foi configurado.

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Perimeter 81 em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Perimeter 81, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Perimeter 81, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

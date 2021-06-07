---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: 0f800d2d42d0d8815021f1582b04750d87aa5abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651379"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Salesforce

Neste tutorial, você aprenderá a integrar o Salesforce ao Azure AD (Azure Active Directory). Ao integrar o Salesforce ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Salesforce.
* Permitir que os usuários sejam conectados automaticamente ao Salesforce com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Salesforce habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Salesforce dá suporte ao SSO iniciado por **SP**.

* O Salesforce dá suporte ao provisionamento e ao desprovisionamento de usuário [**automatizados**](salesforce-provisioning-tutorial.md) (recomendado).

* O Salesforce dá suporte ao provisionamento de usuário **Just In Time**.

* Agora, o aplicativo móvel do Salesforce pode ser configurado com o Azure AD para habilitar o SSO. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

## <a name="adding-salesforce-from-the-gallery"></a>Adicionar Salesforce da galeria

Para configurar a integração do Salesforce ao Azure AD, você precisará adicionar o Salesforce da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Salesforce** na caixa de pesquisa.
1. Selecione **Salesforce** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Configurar e testar o SSO do Azure AD para o Salesforce

Configure e teste o SSO do Azure AD com o Salesforce usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Salesforce.

Para configurar e testar o SSO do Azure AD com o Salesforce, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Salesforce](#configure-salesforce-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    * **[Criar um usuário de teste do Salesforce](#create-salesforce-test-user)** – para ter um equivalente de B. Fernandes no Salesforce que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Salesforce**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:
    
    a. Na caixa de texto **Identificador**, digite o valor usando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Na caixa de texto **URL de resposta**, digite o valor com o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte do Cliente Salesforce](https://help.salesforce.com/support) para obter esses valores.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Salesforce**, copie as URLs apropriadas com base em suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure ao conceder a ela o acesso ao Salesforce.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Salesforce**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-salesforce-sso"></a>Configurar o SSO do Salesforce

1. Para automatizar a configuração no Salesforce, é necessário instalar a **Extensão do navegador de Conexão Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clicar em **Configurar o Salesforce** direcionará você para o aplicativo Salesforce Single Sign-On. Nele, forneça as credenciais de administrador para entrar no Salesforce Single Sign-On. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 13.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o Salesforce manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Salesforce como administrador e execute as seguintes etapas:

1. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar Logon Único ícone de configurações](./media/salesforce-tutorial/configure1.png)

1. Role para baixo até **CONFIGURAÇÕES** no painel de navegação e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar Logon Único Configurações](./media/salesforce-tutorial/sf-admin-sso.png)

1. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Configurar Logon Único Editar](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se não for possível habilitar as configurações de Logon Único para a conta do Salesforce, talvez seja necessário entrar em contato com a [equipe de suporte do Cliente Salesforce](https://help.salesforce.com/support).

1. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Configurar Logon Único SAML Habilitado](./media/salesforce-tutorial/sf-enable-saml.png)

1. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Configurar Logon Único Novo do Arquivo de Metadados](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Clique em **Escolher Arquivo** para carregar o arquivo XML de metadados que você baixou do portal do Azure e clique em **Criar**.

    ![Configurar Logon Único Escolher Arquivo](./media/salesforce-tutorial/xmlchoose.png)

1. Na página **Configurações de SSO do SAML**, os campos são preenchidos automaticamente. Se quiser usar o JIT do SAML, selecione **Provisionamento de usuário habilitado** e selecione **Tipo de identidade do SAML** como **A declaração contém a ID da Federação do objeto de usuário**. Caso contrário, desmarque **Provisionamento de usuário habilitado** e selecione **Tipo de identidade do SAML** como **A declaração contém o nome de usuário do Salesforce**. Clique em **Save** (Salvar).

    ![Configurar Logon Único Provisionamento de Usuário Habilitado](./media/salesforce-tutorial/salesforcexml.png)

1. No painel de navegação à esquerda no Salesforce, clique em **Configurações da Empresa** para expandir a seção correspondente e clique em **Meu Domínio**.

    ![Configurar Logon Único Meu Domínio](./media/salesforce-tutorial/sf-my-domain.png)

1. Role para baixo até a seção **Configuração de Autenticação** e, em seguida, clique no botão **Editar**.

    ![Configurar Logon Único Configuração de Autenticação](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. Na seção **Configuração da autenticação**, marque a **Página de logon** e o **AzureSSO** como o **Serviço de autenticação** de sua configuração de SSO do SAML e clique em **Salvar**.

    ![Configurar Logon Único Serviço de Autenticação](./media/salesforce-tutorial/authentication.png)

    > [!NOTE]
    > Se mais de um serviço de autenticação estiver selecionado, quando os usuários tentarem iniciar o logon único em seu ambiente do Salesforce, serão solicitados a selecionar o serviço de autenticação com o qual desejam entrar. Se você não quiser que isso aconteça, deverá **deixar todos os outros serviços de autenticação desmarcados**.

### <a name="create-salesforce-test-user"></a>Criar um usuário de teste do Salesforce

Nesta seção, é criado um usuário denominado B. Fernandes no Salesforce. O Salesforce dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce, um novo será criado quando você tentar acessar o Salesforce. O Salesforce também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](salesforce-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático do usuário.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Salesforce na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Salesforce diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Salesforce em Meus Aplicativos, você deverá ser conectado automaticamente ao Salesforce para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-salesforce-mobile"></a>Testar o SSO para Salesforce (móvel)

1. Abra o aplicativo móvel do Salesforce. Na página de entrada, clique em **Usar Domínio Personalizado**.

    ![Aplicativo móvel do Salesforce Usar Domínio Personalizado](media/salesforce-tutorial/mobile-app1.png)

1. Na caixa de texto **Domínio Personalizado**, insira o nome de domínio personalizado registrado e clique em **Continuar**.

    ![Aplicativo móvel do Salesforce Domínio Personalizado](media/salesforce-tutorial/mobile-app2.png)

1. Insira suas credenciais do Azure AD para entrar no aplicativo do Salesforce e clique em **Avançar**.

    ![Aplicativo móvel do Salesforce Credenciais do Azure AD](media/salesforce-tutorial/mobile-app3.png)

1. Na página **Permitir Acesso**, conforme mostrado abaixo, clique em **Permitir** para conceder acesso ao aplicativo do Salesforce.

    ![Aplicativo móvel do Salesforce Permitir Acesso](media/salesforce-tutorial/mobile-app4.png)

1. Por fim, após entrar com sucesso, a home page do aplicativo será exibida.

    ![Home page do aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app5.png) ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Salesforce, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
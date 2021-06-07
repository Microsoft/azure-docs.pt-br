---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao LinkedIn Talent Solutions | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Talent Solutions.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: 160c7514b095a330a52dd1607dca6f2eb87215d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727320"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao LinkedIn Talent Solutions

Neste tutorial, você aprenderá a integrar o LinkedIn Talent Solutions ao Azure AD (Azure Active Directory). Ao integrar o LinkedIn Talent Solutions ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao LinkedIn Talent Solutions.
* Permitir que os usuários sejam conectados automaticamente ao LinkedIn Talent Solutions com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Acesso ao Centro de Contas em seu painel do LinkedIn Talent Solutions

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O LinkedIn Talent Solutions é compatível com o SSO iniciado por **SP e IdP**
* O LinkedIn Talent Solutions é compatível com o provisionamento de usuário **Just-in-Time**


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>Adicionar o LinkedIn Talent Solutions da galeria

Para configurar a integração do LinkedIn Talent Solutions Insight ao Azure AD, é necessário adicionar o LinkedIn Talent Solutions da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **LinkedIn Talent Solutions** na caixa de pesquisa.
1. Selecione **LinkedIn Talent Solutions** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>Configurar e testar o SSO do Azure AD para o LinkedIn Talent Solutions

Configure e teste o SSO do Azure AD com o LinkedIn Talent Solutions usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LinkedIn Talent Solutions.

Para configurar e testar o SSO do Azure AD com o LinkedIn Talent Solutions, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do LinkedIn Talent Solutions](#configure-linkedin-talent-solutions-sso)** : para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do LinkedIn Talent Solutions](#create-linkedin-talent-solutions-test-user)** : para ter um equivalente de B.Fernandes no Aruba User Experience Insight que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **LinkedIn Talent Solutions**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    a. Clique em **Carregar arquivo de metadados**.

    ![image1](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![image2](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção Configuração Básica do SAML:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Se os valores de **Identificador** e **URL de resposta** não forem preenchidos automaticamente, preencha-os manualmente de acordo com seus requisitos.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://www.linkedin.com/talent/`

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o LinkedIn Talent Solutions**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao LinkedIn Talent Solutions.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **LinkedIn Talent Solutions**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-linkedin-talent-solutions-sso"></a>Configurar o SSO do LinkedIn Talent Solutions

1. Entre no site do LinkedIn talento Solutions como administrador.
1. Navegue até **Centro de Contas**. 
1. Selecione a guia **Configurações** na barra de navegação.

    ![página de configurações](./media/linkedin-talent-solutions-tutorial/settings.png)

1. Expanda a seção **SSO (Logon Único)** . 

1. Clique no botão **Baixar** para baixar o **arquivo de metadados** ou clique no link **ou clique aqui para carregar e copiar os campos individuais do formulário** para revelar os dados de configuração.

    ![ dados de configuração](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. Execute as etapas a seguir para copiar os campos individuais do formulário.

    ![ configuração usando dados de entrada](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. Copie o valor **ID da Entidade**, cole esse valor na caixa de texto **Identificador do Azure AD** na seção **Configuração Básica de SAML** no portal do Azure.

    b. Copie o valor **URL do ACS**, cole este valor na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    c. Copie o conteúdo da caixa de texto **Certificado de SP X.509(assinatura)** no bloco de notas e salve-o em seu computador.

1. Clique em **Carregar arquivo XML** para carregar o arquivo **XML de Metadados de Federação** que você copiou do portal do Azure.

    ![ Carregar arquivo XML](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>Configurar um usuário de teste do LinkedIn Talent Solutions

Nesta seção, uma usuária chamada Brenda Fernandes será criada no LinkedIn Talent Solutions. O LinkedIn Talent Solutions é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no LinkedIn Talent Solutions, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do LinkedIn Talent Solutions, em que você pode iniciar o fluxo de logon.  

* Acesse a URL de Logon do LinkedIn Talent Solutions diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao LinkedIn Talent Solutions, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do LinkedIn Talent Solutions em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao LinkedIn Talent Solutions, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o LinkedIn Talent Solutions, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
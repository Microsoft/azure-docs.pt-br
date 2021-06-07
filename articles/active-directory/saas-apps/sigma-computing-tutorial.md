---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Sigma Computing | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sigma Computing.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 8d28ceb5b323a811f44599f9d4f0205c6aaf4f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647011"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sigma-computing"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Sigma Computing

Neste tutorial, você aprenderá a integrar o Sigma Computing ao Azure AD (Azure Active Directory). Quando você integrar o Sigma Computing ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Sigma Computing.
* Permitir que os usuários sejam conectados automaticamente ao Sigma Computing com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Sigma Computing.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Sigma Computing é compatível com o SSO iniciado por **SP e IdP**
* O Sigma Computing é compatível com o provisionamento de usuário **Just In Time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-sigma-computing-from-the-gallery"></a>Adicionar o Sigma Computing da galeria

Para configurar a integração do Sigma Computing ao Azure AD, você precisa adicionar o Sigma Computing da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Sigma Computing** na caixa de pesquisa.
1. Selecione **Sigma Computing** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-sigma-computing"></a>Configurar e testar o SSO do Azure AD para o Sigma Computing

Configure e teste o SSO do Azure AD com o Sigma Computing usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Sigma Computing.

Para configurar e testar o SSO do Azure AD com o Sigma Computing, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Sigma Computing](#configure-sigma-computing-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Sigma Computing](#create-sigma-computing-test-user)** – para ter um equivalente de B.Fernandes no Sigma Computing vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Sigma Computing**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL usando um dos seguintes padrões:

    | URL de logon |
    |------------|
    | `https://app.sigmacomputing.com/<CustomerOrg>`|
    | `https://aws.sigmacomputing.com/<CustomerOrg>`|

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do Sigma Computing](mailto:support@sigmacomputing.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Clique em **Save** (Salvar).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Sigma Computing**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Sigma Computing.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Sigma Computing**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sigma-computing-sso"></a>Configurar o SSO do Sigma Computing

1. Faça logon na sua conta do Sigma.

1. Navegue até o **Portal de Administração** selecionando **Administração** no menu de usuário.

1. Execute as etapas a seguir na página abaixo.

    ![Seção Configurar SSO do Sigma Computing](./media/sigma-computing-tutorial/authentication.png)

    a. Selecione a página **Autenticação** no painel esquerdo.

    b. Em **Método de Autenticação**, selecione **SAML** ou **SAML ou senha**.

    c. Na caixa de texto **URL de logon do provedor de identidade**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de Notas e cole o conteúdo na caixa de texto **Certificado X509 do Provedor de Identidade**.

    e.  Clique em **Save**.

### <a name="create-sigma-computing-test-user"></a>Criar usuário de teste do Sigma Computing

Nesta seção, é criado um usuário denominado Brenda Fernandes no Sigma Computing. O Sigma Computing é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Sigma Computing, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso vai redirecionar você à URL de Logon do Sigma Computing na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do Sigma Computing diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Sigma Computing, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Sigma Computing em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Sigma Computing, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Sigma Computing, você poderá impor controles de sessão, que fornecem proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
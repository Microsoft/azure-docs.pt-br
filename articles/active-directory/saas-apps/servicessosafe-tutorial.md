---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SoSafe | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SoSafe.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 046ded816c28c2153e5609992eb4b7c7cd7f2978
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729555"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sosafe"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SoSafe

Neste tutorial, você aprenderá a integrar o SoSafe ao Azure AD (Azure Active Directory). Ao integrar o SoSafe ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SoSafe.
* Permitir que os usuários sejam automaticamente conectados ao SoSafe com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SoSafe.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SoSafe dá suporte ao SSO iniciado por **SP e IdP**
* O SoSafe dá suporte ao provisionamento de usuário **Just-In-Time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.


## <a name="adding-sosafe-from-the-gallery"></a>Como adicionar o SoSafe por meio da galeria

Para configurar a integração do SoSafe ao Azure AD, você precisará adicionar o SoSafe por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite SoSafe na caixa de pesquisa.
1. Selecione SoSafe no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-sosafe"></a>Configurar e testar o SSO do Azure AD para o SoSafe

Configure e teste o SSO do Azure AD com o SoSafe usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SoSafe.

Para configurar e testar o SSO do Azure AD com o SoSafe, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SoSafe](#configure-sosafe-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SoSafe](#create-sosafe-test-user)** : para ter um equivalente de B.Fernandes no SoSafe que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do SoSafe, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://api.sosafe.de/v1/auth/saml/login/<TENANT_ID>`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do SoSafe](mailto:support@sosafe.de) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção Configurar o SoSafe, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao SoSafe.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione SoSafe.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sosafe-sso"></a>Configurar o SSO do SoSafe

1. Em outra janela do navegador da Web, entre no site do SoSafe como administrador.

1. Clique em **Dados Estendidos** e execute essas etapas na página a seguir.

    ![Página SAML de dados estendidos](./media/servicessosafe-tutorial/saml-settings.png)

    a. Cole o valor da ID de Locatário na caixa de texto **ID de Locatário do Azure** do portal do Azure.

    b. Abra o **Cerificate(Base64)** baixado do portal do Azure no Bloco de Notas e cole o conteúdo na caixa de texto **Certificado**.

    c. Na caixa **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa **Identificador do Azure AD**, cole o valor da **ID da Entidade** copiado do portal do Azure.

    e. Na caixa **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    f. Clique em **SALVAR**

### <a name="create-sosafe-test-user"></a>Criar um usuário de teste do SoSafe

Nesta seção, um usuário chamado Brenda Fernandes será criado no SoSafe. O SoSafe dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no SoSafe, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do SoSafe, na qual poderá iniciar o fluxo de logon.  

1. Acesse diretamente a URL de Logon do SoSafe e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você deverá ser conectado automaticamente ao SoSafe, para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do SoSafe no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao SoSafe, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SoSafe, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
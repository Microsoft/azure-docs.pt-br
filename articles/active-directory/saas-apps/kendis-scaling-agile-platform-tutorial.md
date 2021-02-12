---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Kendis-Scaling Agile Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Kendis-Scaling Agile Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: e02ff4926897fafc72e1a5081366faad5d2f03ba
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509601"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis-scaling-agile-platform"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Kendis-Scaling Agile Platform

Neste tutorial, você aprenderá a integrar o Kendis-Scaling Agile Platform ao Azure AD (Azure Active Directory). Ao integrar o Kendis-Scaling Agile Platform ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Kendis-Scaling Agile Platform.
* Permitir que os usuários sejam conectados automaticamente ao Kendis-Scaling Agile Platform com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Kendis-Scaling Agile Platform.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Kendis-Scaling Agile Platform é compatível com SSO iniciado por **SP e IdP**
* Ele também é compatível com o provisionamento de usuário **Just-In-Time**


## <a name="adding-kendis-scaling-agile-platform-from-the-gallery"></a>Adicionando o Kendis-Scaling Agile Platform da galeria

Para configurar a integração do Kendis-Scaling Agile Platform ao Azure AD, você precisará adicionar o Kendis-Scaling Agile Platform da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Kendis-Scaling Agile Platform** na caixa de pesquisa.
1. Selecione **Kendis-Scaling Agile Platform** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-kendis-scaling-agile-platform"></a>Configurar e testar o SSO do Azure AD para o Kendis-Scaling Agile Platform

Configure e teste o SSO do Azure AD com o Kendis-Scaling Agile Platform usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kendis-Scaling Agile Platform.

Para configurar e testar o SSO do Azure AD com o Kendis-Scaling Agile Platform, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Kendis-Scaling Agile Platform](#configure-kendis-scaling-agile-platform-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Kendis-Scaling Agile Platform](#create-kendis-scaling-agile-platform-test-user)** – para ter um equivalente de B.Fernandes no Kendis-Scaling Agile Platform que esteja vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Kendis-Scaling Agile Platform**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.kendis.io`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do Kendis-Scaling Agile Platform](mailto:support@kendis.io) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Kendis-Scaling Agile Platform**, copie as URLs apropriadas conforme suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Kendis-Scaling Agile Platform.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Kendis-Scaling Agile Platform**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-kendis-scaling-agile-platform-sso"></a>Configurar o SSO do Kendis-Scaling Agile Platform

1. Em outra janela do navegador da Web, entre em seu site de empresa do Kendis-Scaling Agile Platform como administrador.

1. Vá até **Configurações > Configurações de SAML**.

    ![configurações para Configurações de SAML](./media/kendis-scaling-agile-platform-tutorial/settings.png)

1. Clique no botão **Editar** na parte inferior da página e execute as etapas a seguir.

    ![Configurações de SAML](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Copie o valor da **URL de retorno de chamada**, cole este valor na caixa de texto **URL de Resposta** na seção Configuração Básica de SAML no portal do Azure.

    b. Na caixa de texto **URL de logon único de provedor de identidade**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    c.  Na caixa de texto **Emissor do Provedor de Identidade**, cole o valor do **Identificador do Azure AD (ID da Entidade)** copiado do portal do Azure.

    d. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado X.509**.

    e. **Selecione Grupo Padrão** na lista de opções. 

    f. Clique em **Save** (Salvar).

### <a name="create-kendis-scaling-agile-platform-test-user"></a>Criar usuário de teste do Kendis-Scaling Agile Platform

Nesta seção, um usuário chamado Brenda Fernandes será criado no Kendis-Scaling Agile Platform. O Kendis-Scaling Agile Platform dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no Kendis-Scaling Agile Platform, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de logon do Kendis-Scaling Agile Platform, na qual você poderá iniciar o fluxo de logon.  

* Acesse a URL de logon do Kendis-Scaling Agile Platform diretamente e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Kendis-Scaling Agile Platform, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Kendis-Scaling Agile Platform em Meus Aplicativos, se você estiver configurado no modo SP, será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon. Se você estiver configurado no modo IdP, será conectado automaticamente ao Kendis-Scaling Agile Platform, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Kendis-Scaling Agile Platform, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).



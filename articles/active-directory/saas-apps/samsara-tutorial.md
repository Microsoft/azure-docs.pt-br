---
title: 'Tutorial: integração do SSO (logon único) do Azure Active Directory ao Samsara | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Samsara.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: jeedes
ms.openlocfilehash: 9512287f656c6c64399cb8749b7451a5a780bba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677633"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsara"></a>Tutorial: integração do SSO (logon único) do Azure Active Directory ao Samsara

Neste tutorial, você aprenderá a integrar o Samsara ao Azure AD (Azure Active Directory). Ao integrar o Samsara ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Samsara.
* Permitir que os usuários sejam conectados automaticamente ao Samsara com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Samsara.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Samsara é compatível com SSO iniciado por **SP** e **IDP**
* O Samsara é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-samsara-from-the-gallery"></a>Adicionar Samsara da galeria

Para configurar a integração do Samsara ao Azure AD, você precisará adicionar o Samsara da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)
    
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.

    ![O botão Novo aplicativo](common/add-new-app.png)

1. Na seção **Adicionar da galeria**, digite **Samsara** na caixa de pesquisa.

     ![Software de gerenciamento de privacidade OneTrust na lista de resultados](common/search-new-app.png)

1. Selecione **Samsara** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-samsara"></a>Configurar e testar o SSO do Azure AD para o Samsara

Configure e teste o SSO do Azure AD com o Samsara usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente do Samsara.

Para configurar e testar o SSO do Azure AD com o Samsara, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Samsara](#configure-samsara-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Samsara](#create-samsara-test-user)** – Para ter um equivalente de B.Fernandes no Samsara que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Samsara**, localize a seção **Gerenciar** e selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

1. Na página **Selecionar um método de logon único**, escolha **SAML**.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão: `https://cloud.samsara.com/signin/<ORGID>` para clientes de nuvem dos EUA e `https://cloud.eu.samsara.com/signin/<ORGID>` para clientes de nuvem da Europa

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `urn:auth0:samsara-dev:samlp-orgid-<ORGID>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://samsara-dev.auth0.com/login/callback?connection=samlp-orgid-<ORGID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, a URL de Resposta e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do Samsara](mailto:support@samsara.com) para obter esses valores ou, no Samsara, vá para **Configurações** > **Logon único** > **Nova conexão SAML** para obter o \<ORGID\>. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar Samsara**, copie a **URL de logon**

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

Nesta seção, você habilitará B.Fernandes para usar o logon único do Azure permitindo acesso ao Samsara.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Samsara**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-samsara-sso"></a>Configurar o SSO do Samsara

Para configurar o logon único no lado do **Samsara**, é necessário enviar o **Certificado (Base64)** baixado e a **URL de logon** do portal do Azure para a [equipe de suporte do Samsara](mailto:support@samsara.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-samsara-test-user"></a>Criar usuário de teste do Samsara

Nesta seção, um usuário chamado B.Fernandes será criado no Samsara. O Samsara é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se o usuário ainda não existir no Samsara, ele será criado após a autenticação com uma função padrão de Administrador Padrão (sem acesso à câmera do dashboard) da Organização. Então, o acesso do usuário poderá ser aumentado ou diminuído conforme necessário no Samsara.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Samsara, na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de Logon do Samsara diretamente e inicie dela o fluxo de logon.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco do Samsara no Painel de Acesso, você será redirecionado para a URL de Logon do Samsara. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Samsara, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
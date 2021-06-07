---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao raum]für[raum | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o raum]für[raum.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: 29040795f16ee804e2413b52d2692b38ab0f814c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92510552"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raumfrraum"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao raum]für[raum

Neste tutorial, você aprenderá a integrar o raum]für[raum ao Azure AD (Azure Active Directory). Ao integrar o raum]für[raum ao Azure AD, você poderá:

* Controlar quem tem acesso ao raum]für[raum no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao raum]für[raum com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do raum]für[raum habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O raum]für[raum dá suporte ao SSO iniciado por **SP e IdP**
* O raum]für[raum dá suporte ao provisionamento de usuário **just-in-time**

## <a name="add-raumfrraum-from-the-gallery"></a>Adicionar o raum]für[raum por meio da galeria

Para configurar a integração do raum]für[raum ao Azure AD, você precisará adicionar o raum]für[raum por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **raum]für[raum** na caixa de pesquisa.
1. Selecione **raum]für[raum** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-raumfrraum"></a>Configurar e testar o SSO do Azure AD para o raum]für[raum

Configure e teste o SSO do Azure AD com o raum]für[raum usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do raum]für[raum.

Para configurar e testar o SSO do Azure AD com o raum]für[raum, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do raum]für[raum](#configure-raumfurraum-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do raum]für[raum](#create-raumfurraum-test-user)** : para ter um equivalente de B.Fernandes no raum]für[raum que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **raum]für[raum**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL usando um dos seguintes padrões:

    |Identificador|
    |----------|
    |`<CUSTOMER_NAME>.raumfuerraum.de`|
    |`<CUSTOMER_NAME>.rfr.md.intra`|

    b. Na caixa de texto **URL de Resposta**, digite uma URL nos seguintes padrões:

    |URL de resposta|
    |----------|
    |`https://<CUSTOMER_NAME>.raumfuerraum.de`|
    |`https://<CUSTOMER_NAME>.rfr.md.intra`|

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL usando um dos seguintes padrões:

    |URL de logon|
    |----------|
    |`https://<CUSTOMER_NAME>.raumfuerraum.de/saml.php`|
    |`https://<CUSTOMER_NAME>.rfr.md.intra/saml.php`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do raum]für[raum](mailto:it@mediadialog.de) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o raum]für[raum**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao raum]für[raum.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **raum]für[raum**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-raumfurraum-sso"></a>Configurar o SSO do raum]für[raum

Para configurar o logon único no lado do **raum]für[raum**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do raum]für[raum](mailto:it@mediadialog.de). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-raumfurraum-test-user"></a>Criar um usuário de teste do raum]für[raum

Nesta seção, um usuário chamado Brenda Fernandes será criado no raum]für[raum. O raum]für[raum dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no raum]für[raum, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure para ser redirecionado para a URL de Logon do raum]für[raum, na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do raum]für[raum diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no raum]für[raum, para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco raum]für[raum no Painel de Acesso, se estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se estiver configurado no modo IdP, você será conectado automaticamente ao raum]für[raum, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas
Depois de configurar o raum]für[raum, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
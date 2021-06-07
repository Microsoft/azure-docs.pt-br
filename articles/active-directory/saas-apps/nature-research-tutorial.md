---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Nature Research | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Nature Research.
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
ms.openlocfilehash: 8996d030a9593645bd8e3a6489cb5b7bd00d557d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181944"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nature-research"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Nature Research

Neste tutorial, você aprenderá a integrar o Nature Research ao Azure AD (Azure Active Directory). Com a integração do Nature Research ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao Nature Research.
* Permitir que seus usuários sejam conectados automaticamente ao Nature Research com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Nature Research.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Nature Research é compatível com SSO iniciado por **SP e IDP**

## <a name="adding-nature-research-from-the-gallery"></a>Como adicionar o Nature Research por meio da galeria

Para configurar a integração do Nature Research com o Azure AD, você precisa adicionar o Nature Research da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Nature Research** na caixa de pesquisa.
1. Selecione **Nature Research** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-nature-research"></a>Configurar e testar o SSO do Azure AD para o Nature Research

Configure e teste o SSO do Azure AD com o Nature Research usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Nature Research.

Para configurar e testar o SSO do Azure AD com o Nature Research, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Nature Research](#configure-nature-research-sso)** – para definir as configurações de logon único no lado do aplicativo.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Nature Research**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, se desejar configurar o aplicativo no modo iniciado por **IDP**, os valores de Identificador e URL de Resposta já serão populados previamente com o Azure, mas você precisará inserir o valor do Estado de Retransmissão.

    Na caixa de texto **Estado de Retransmissão**, digite uma URL: `https://www.nature.com`  
    e clique em **Salvar**.

1. Clique em **Definir URLs adicionais** e execute as seguintes etapas caso deseje configurar o aplicativo no modo iniciado por **SP**:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://sp.nature.com/saml/login?idp=<IDP_ENTITY_ID>`

    > [!NOTE]
    > O valor da URL de logon não é real. `<IDP_ENTITY_ID>` é o Identificador do Azure AD copiado da seção **Configurar o Nature Research**. Veja também os padrões mostrados na seção "Configuração Básica de SAML" no portal do Azure.

1. Clique em **Save** (Salvar).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Nature Research.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Nature Research**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-nature-research-sso"></a>Configurar o SSO do Nature Research

Para configurar o logon único no lado do **Nature Research**, é necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do Nature Research](mailto:onlineservice@springernature.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Nature Research, na qual você poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do Nature Research diretamente e inicie dela o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Nature Research para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco Nature Research no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Nature Research para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Nature Research, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
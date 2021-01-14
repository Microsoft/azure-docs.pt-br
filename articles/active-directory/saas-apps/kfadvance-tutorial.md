---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao KFAdvance | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o KFAdvance.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: jeedes
ms.openlocfilehash: 241e5abf8377bd2e2207d140e291b18592651ea6
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179581"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kfadvance"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao KFAdvance

Neste tutorial, você aprenderá a integrar o KFAdvance ao Azure AD (Azure Active Directory). Ao integrar o KFAdvance ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao KFAdvance.
* Permitir que os usuários sejam conectados automaticamente ao KFAdvance com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do KFAdvance habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O KFAdvance é compatível com o SSO iniciado por **SP e IdP**

## <a name="adding-kfadvance-from-the-gallery"></a>Adicionar o KFAdvance da galeria

Para configurar a integração do KFAdvance ao Azure AD, é necessário adicionar o KFAdvance da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **KFAdvance** na caixa de pesquisa.
1. Selecione **KFAdvance** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-kfadvance"></a>Configurar e testar o SSO do Azure AD para o KFAdvance

Configure e teste o SSO do Azure AD com o KFAdvance usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no KFAdvance.

Para configurar e testar o SSO do Azure AD com o KFAdvance, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do KFAdvance](#configure-kfadvance-sso)** : para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do KFAdvance](#create-kfadvance-test-user)** : para ter um equivalente de B.Fernandes no KFAdvance que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **KFAdvance**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.kfadvance.com/<PARTNER_ID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.kfadvance-<ENVIRONMENT>.com/v1/account/partnerssocallback?partnerKey=<PARTNER_ID>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://api.kfadvance.com/v1/account/partnerssologin?partnerKey=<PARTNER_ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do ao cliente do KFAdvance](mailto:support@kornferry.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o KFAdvance**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao KFAdvance.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **KFAdvance**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-kfadvance-sso"></a>Configurar o SSO do KFAdvance

Para configurar o logon único no lado do **KFAdvance**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do KFAdvance](mailto:support@kornferry.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-kfadvance-test-user"></a>Criar um usuário de teste do KFAdvance

Nesta seção, você criará uma usuária chamada Brenda Fernandes no KFAdvance. Trabalhe com a [equipe de suporte do KFAdvance](mailto:support@kornferry.com) para adicionar os usuários na plataforma do KFAdvance. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do KFAdvance, em que você pode iniciar o fluxo de logon.  

* Acesse a URL de Logon do KFAdvance diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao KFAdvance, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do KFAdvance em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao KFAdvance, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o KFAdvance, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).



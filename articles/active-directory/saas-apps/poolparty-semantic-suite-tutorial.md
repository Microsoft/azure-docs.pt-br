---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PoolParty Semantic Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PoolParty Semantic Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 3cd76eb3e6313b21bbc018bc14287c38517537f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645455"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-poolparty-semantic-suite"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PoolParty Semantic Suite

Neste tutorial, você aprenderá a integrar o PoolParty Semantic Suite ao Azure AD (Azure Active Directory). Ao integrar o PoolParty Semantic Suite ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao PoolParty Semantic Suite.
* Permitir que os usuários sejam conectados automaticamente ao PoolParty Semantic Suite com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do PoolParty Semantic Suite habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O PoolParty Semantic Suite dá suporte ao SSO iniciado por **SP**

## <a name="adding-poolparty-semantic-suite-from-the-gallery"></a>Adicionando o PoolParty Semantic Suite da galeria

Para configurar a integração do PoolParty Semantic Suite ao Azure AD, é necessário adicioná-lo por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **PoolParty Semantic Suite** na caixa de pesquisa.
1. Selecione **PoolParty Semantic Suite** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-poolparty-semantic-suite"></a>Configurar e testar o SSO do Azure AD para o PoolParty Semantic Suite

Configure e teste o SSO do Azure AD com o PoolParty Semantic Suite usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PoolParty Semantic Suite.

Para configurar e testar o SSO do Azure AD com o PoolParty Semantic Suite, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do PoolParty Semantic Suite](#configure-poolparty-semantic-suite-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do PoolParty Semantic Suite](#create-poolparty-semantic-suite-test-user)** : para ter um equivalente de B.Fernandes no PoolParty Semantic Suite que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **PoolParty Semantic Suite**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<CustomerName>.poolparty.biz/PoolParty/`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<CustomerName>.poolparty.biz/<ID>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CustomerName>.poolparty.biz/<ID>`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do PoolParty Semantic Suite](mailto:support@poolparty.biz) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o PoolParty Semantic Suite**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao PoolParty Semantic Suite.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **PoolParty Semantic Suite**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-poolparty-semantic-suite-sso"></a>Configurar o SSO do PoolParty Semantic Suite

Para configurar o logon único no lado do **PoolParty Semantic Suite**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do PoolParty Semantic Suite](mailto:support@poolparty.biz). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-poolparty-semantic-suite-test-user"></a>Criar usuário de teste do PoolParty Semantic Suite

Nesta seção, você criará uma usuária chamada Brenda Fernandes no PoolParty Semantic Suite. Trabalhe com a [equipe de suporte do PoolParty Semantic Suite](mailto:support@poolparty.biz) para adicionar os usuários na plataforma do PoolParty Semantic Suite. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do PoolParty Semantic Suite, na qual você poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do PoolParty Semantic Suite diretamente e inicie o fluxo de logon nele.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do PoolParty Semantic Suite em Meus Aplicativos, será redirecionado para a URL de Logon do PoolParty Semantic Suite. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o PoolParty Semantic Suite, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do Azure Active Directory ao New Relic by Account | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o New Relic by Account.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: a2c149bfdf79102779abf7544fed9fb78796a50e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649935"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao New Relic by Account

Neste tutorial, você aprenderá a integrar o New Relic by Account ao Azure AD (Azure Active Directory). Ao integrar o New Relic by Account ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao New Relic by Account.
* Permitir que os usuários sejam conectados automaticamente ao New Relic by Account com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do New Relic by Account com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O New Relic by Account é compatível com SSO iniciado por **SP**

## <a name="add-new-relic-by-account-from-the-gallery"></a>Adicionar o New Relic by Account por meio da galeria

Para configurar a integração do New Relic by Account ao Azure AD, você precisará adicionar o New Relic by Account por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **New Relic by Account** na caixa de pesquisa.
1. Selecione **New Relic by Account** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-new-relic-by-account"></a>Configurar e testar o SSO do Azure AD para o New Relic by Account

Configure e teste o SSO do Azure AD com o New Relic by Account usando um(a) usuário(a) de teste com o nome **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do New Relic by Account.

Para configurar e testar o SSO do Azure AD com o New Relic by Account, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do New Relic by Account](#configure-new-relic-by-account-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do New Relic by Account](#create-new-relic-by-account-test-user)** – para ter um equivalente de B.Fernandes no New Relic by Account que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **New Relic by Account**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)
   
1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite a URL usando o seguinte padrão: 

    `https://rpm.newrelic.com:443/accounts/{acc_id}/sso/saml/finalize` – Substitua `acc_id` pela sua ID da conta do New Relic by Account.

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `rpm.newrelic.com`

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o New Relic by Account**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao New Relic by Account.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **New Relic by Account**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name=&quot;configure-new-relic-by-account-sso&quot;></a>Configurar o SSO do New Relic by Account

1. Em outra janela do navegador da Web, entre em seu site de empresa do **New Relic by Account** como administrador.

2. No menu na parte superior, clique em **Configurações de Conta**.
   
    ![A captura de tela mostra a página inicial com a opção Configurações de Conta selecionada.](./media/new-relic-tutorial/settings.png &quot;Configurações da Conta")

3. Clique na guia **Segurança e autenticação** e na guia **Logon único**.
   
    ![Logon único](./media/new-relic-tutorial/single-sign-on-tab.png "Logon Único")

4. Na página da caixa de diálogo SAML, execute as seguintes etapas:
   
    ![SAML](./media/new-relic-tutorial/save.png "SAML")
   
    a. Clique em **Escolher Arquivo** para carregar seu certificado baixado do Active Directory do Azure.

    b. Na caixa de texto **URL de logon remoto**, cole o valor da **URL de Logon** copiado do portal do Azure.
   
    c. Na caixa de texto **URL de aterrissagem de logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Clique em **Salvar minhas alterações**.

### <a name="create-new-relic-by-account-test-user"></a>Criar usuário de teste do New Relic by Account

1. Faça logon em seu site de empresa do **New Relic by Account** como administrador.

2. No menu na parte superior, clique em **Configurações de Conta**.
   
    ![A captura de tela mostra a opção Configurações de conta selecionada na página inicial.](./media/new-relic-tutorial/account.png "Configurações da Conta")

3. No painel **Conta** no lado esquerdo, clique em **Resumo** e em **Adicionar usuário**.
   
    ![A captura de tela mostra o painel Resumo em que você pode selecionar Adicionar usuário.](./media/new-relic-tutorial/add.png "Configurações da Conta")

4. No diálogo **Usuários ativos** , realize as seguintes etapas:
   
    ![Usuários ativos](./media/new-relic-tutorial/user.png "Usuários ativos")
   
    a. Na caixa de texto **Email** , digite o endereço de email de um usuário válido do Active Directory do Azure que você deseja provisionar.

    b. Como **Função**, selecione **Usuário**.

    c. Clique em **Adicionar este usuário**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do New Relic by Account ou as APIs fornecidas pelo New Relic by Account para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do New Relic by Account, na qual será possível iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do New Relic by Account e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do New Relic by Account em Meus Aplicativos, você será redirecionado para a URL de Logon do New Relic by Account. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o New Relic by Account, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
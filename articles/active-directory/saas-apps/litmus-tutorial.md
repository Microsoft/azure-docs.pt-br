---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Litmus | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Litmus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: f2168715d59f9698cba58b7e91bbc897a8cd94f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727226"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Litmus

Neste tutorial, você aprenderá a integrar o Litmus ao Azure AD (Azure Active Directory). Ao integrar o Litmus ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao Litmus.
* Permitir que seus usuários sejam conectados automaticamente ao Litmus com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Litmus habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Litmus é compatível com SSO iniciado por **SP e IDP**

## <a name="adding-litmus-from-the-gallery"></a>Adicionar o Litmus da galeria

Para configurar a integração do Litmus ao Azure AD, é necessário adicionar o Litmus da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Litmus** na caixa de pesquisa.
1. Selecione **Litmus** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-litmus"></a>Configurar e testar o SSO do Azure AD para o Litmus

Configure e teste o SSO do Azure AD com o Litmus usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Litmus.

Para configurar e testar o SSO do Azure AD com o Litmus, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Litmus](#configure-litmus-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Litmus](#create-litmus-test-user)** para ter um equivalente de B.Fernandes no Litmus que seja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Litmus**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://litmus.com/sessions/new`

1. Clique em **Save** (Salvar).

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Bruto)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificateraw.png)

1. Na seção **Configurar o Litmus**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Litmus.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Litmus**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-litmus-sso"></a>Configurar o SSO do Litmus

1. Para automatizar a configuração no Litmus, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, clique em **Configurar o Litmus** e você será direcionado ao aplicativo Litmus. Nele, forneça as credenciais de administrador para entrar no Litmus. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Litmus manualmente, em uma janela diferente do navegador da Web, entre no site da empresa do Litmus como administrador.

1. Clique em **Segurança** do painel de navegação esquerdo.

    ![A captura de tela mostra o item Segurança selecionado.](./media/litmus-tutorial/security-img.png)

1. Na seção **Configurar Autenticação SAML**, siga estas etapas:

    ![A captura de tela mostra a seção Configurar Autenticação SAML em que você pode inserir os valores descritos.](./media/litmus-tutorial/configure1.png)

    a. Ative a opção **Habilitar SAML**.

    b. Selecione **Genérico** para o provedor.

    c. Insira o nome do **Provedor de Identidade**. por ex. `Azure AD`

1. Execute as seguintes etapas:

    ![A captura de tela mostra a seção em que você pode inserir os valores descritos.](./media/litmus-tutorial/configure3.png)

    a. Na caixa de texto **Ponto de Extremidade do SAML 2.0 (HTTP)** , cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Abra o arquivo do **Certificado** baixado do portal do Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **Certificado X.509**.

    c. Clique em **Salvar configurações do SAML**.

### <a name="create-litmus-test-user"></a>Criar um usuário de teste do Litmus

1. Em outra janela do navegador da Web, entre no aplicativo do Litmus como administrador.

1. Clique nas **Contas** no painel de navegação esquerdo.

    ![A captura de tela mostra o item Conta selecionado.](./media/litmus-tutorial/accounts-img.png)

1. Clique na guia **Adicionar Novo Usuário**.

    ![A captura de tela mostra o item Adicionar Novo Usuário selecionado.](./media/litmus-tutorial/add-new-user.png)

1. Na seção **Adicionar Usuário**, siga estas etapas:

    ![A captura de tela mostra a seção Adicionar Usuário em que você pode inserir os valores descritos.](./media/litmus-tutorial/user-profile.png)

    a. Na caixa de texto **Email**, insira o endereço de email do usuário como **B.Fernandes\@contoso.com**

    b. Na caixa de texto **Nome**, insira o nome do usuário como **B**.

    c. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    d. Clique em **Criar Usuário**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Litmus, na qual poderá iniciar o fluxo de logon.

* Acesse a URL de Logon do Litmus diretamente e inicie dela o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você deverá ser conectado automaticamente ao Litmus para o qual configurou o SSO

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Litmus nos Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Litmus para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Litmus, você poderá impor o controle de sessão, que protege contra o vazamento e a infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
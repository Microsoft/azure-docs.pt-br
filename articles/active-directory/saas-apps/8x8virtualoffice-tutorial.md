---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o 8x8 | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o 8x8.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 81a7efea268600e661981b35f79149fe814ef084
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180669"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o 8x8

Neste tutorial, você aprenderá a integrar o 8x8 ao Azure AD (Azure Active Directory). Ao integrar o 8x8 ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao 8x8.
* Permitir que os usuários sejam conectados automaticamente ao 8x8 com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do 8x8.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O 8x8 é compatível com SSO iniciado por **SP e IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-8x8-from-the-gallery"></a>Adicionando o 8x8 por meio da galeria

Para configurar a integração do 8x8 ao Azure AD, você precisa adicionar o 8x8 por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **8x8** na caixa de pesquisa.
1. Selecione **8x8** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>Configurar e testar o SSO do Azure AD para o 8x8

Configure e teste o SSO do Azure AD com o 8x8 usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do 8x8.

Para configurar e testar o SSO do Azure AD com o 8x8, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do 8x8](#configure-8x8-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do 8x8](#create-8x8-test-user)** – para ter um equivalente de B.Fernandes no 8x8 que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **8x8**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **Identificador**, digite uma URL: `https://sso.8x8.com/saml2`

    b. Na caixa de texto **URL de Resposta**, insira uma URL: `https://sso.8x8.com/saml2`

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador. Você usará o certificado posteriormente no tutorial na seção **Configurar o SSO do 8x8**.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o 8x8**, copie as URLs, pois você usará esses valores de URL posteriormente no tutorial.

    ![Copiar URLs de configuração](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao 8x8.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **8x8**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-8x8-sso"></a>Configurar o SSO do 8x8

A próxima parte do tutorial depende do tipo de assinatura que você tem com o 8x8.

* Para os clientes das Edições e das Séries X do 8x8 que usam Configuration Manager para administração, confira [Configurar o Console de Administração do 8x8](#configure-8x8-admin-console).
* Para clientes do Virtual Office que usam o Gerenciador de Contas para administração, confira [Configurar o Gerenciador de Contas do 8x8](#configure-8x8-account-manager).

### <a name="configure-8x8-admin-console"></a>Configurar o Console de Administração do 8x8

1. Para automatizar a configuração no 8x8, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o 8x8** para ser direcionado ao aplicativo 8x8. Nele, forneça as credenciais de administrador para entrar no 8x8. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o 8x8 manualmente, entre no [Console de Administração](https://admin.8x8.com/) do 8x8 como administrador.

1. Na página inicial clique em **Gerenciamento de Identidades**.

    ![Captura de tela que realça o bloco Gerenciamento de Identidades.](./media/8x8virtualoffice-tutorial/configure1.png)

1. Marque **SSO (Logon Único)** e, em seguida, selecione **Microsoft Azure AD**.

    ![Captura de tela que realça as opções SSO (Logon Único) e Microsoft Azure AD.](./media/8x8virtualoffice-tutorial/configure2.png)

1. Copie as três URLs e o certificado de autenticação da página **Configurar Logon Único com SAML** no Azure AD, na seção **Configurações de SAML do Microsoft Azure AD** no Console de Administração do 8x8.

    ![Console de Administração do 8x8](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Copie a **URL de Logon** para a **URL de Logon de IDP**.

    b. Copie o **Identificador do Azure AD** para **URL/URN do emissor de IDP**.

    c. Copie a **URL de Logout** para a **URL de Logout de IDP**.

    d. Baixe o **Certificado (Base64)** e carregue-o em **Certificado**.

    e. Clique em **Save** (Salvar).

### <a name="configure-8x8-account-manager"></a>Configurar o Gerenciador de Contas do 8x8

1. Faça logon no seu locatário do 8x8 Virtual Office como administrador.

1. Selecione **Virtual Office Account Mgr** no painel do aplicativo.

    ![Captura de tela que realça o bloco do Virtual Office Account Mgr.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Selecione a **conta comercial** que deseja gerenciar e clique no botão **Entrar**.

    ![Captura de tela que realça a opção Comercial e o botão Entrar.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Clique em **contas** guia na lista de menus.

    ![Captura de tela que realça a guia CONTAS na lista de menus.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Clique em **Single Sign On** na lista Accounts.

    ![Captura de tela que realça a opção Logon Único.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Selecione **Logon único** em Métodos de autenticação e clique em **SAML**.

    ![Captura de tela que realça o SAML em Logon Único.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. No **logon único do SAML** , realize as seguintes etapas:

    ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Na caixa de texto **URL de login**, cole o **valor do URL de login** que você copiou do portal do Azure.

    b. No **URL de logoff** caixa de texto, cole **URL de logoff** valor que você copiou do portal do Azure.

    c. Na caixa de texto **URL do emissor**, cole o valor **Identificador do Azure AD** que você copiou do portal do Azure.

    d. Clique em **procurar** botão para carregar o certificado que você baixou do portal do Azure.

    e. Clique no botão **Salvar** .

### <a name="create-8x8-test-user"></a>Criar usuário de teste do 8x8

Nesta seção, você criará uma usuária chamada Brenda Fernandes no 8x8. Trabalhe com a [equipe de suporte do 8x8](https://www.8x8.com/about-us/contact-us) para adicionar os usuários à plataforma do 8x8. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do 8x8, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do 8x8, na qual você iniciará o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao 8x8 para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco 8x8 no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao 8x8 para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o 8x8, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
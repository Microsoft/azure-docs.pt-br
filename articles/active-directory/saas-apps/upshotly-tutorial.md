---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Upshotly | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Upshotly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/5/2021
ms.author: jeedes
ms.openlocfilehash: 53b4702bd4ac5b4fe608eb7a0e161f36d6cb4cb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726909"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-upshotly"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Upshotly

Neste tutorial, você aprenderá como integrar o Upshotly ao Azure AD (Azure Active Directory). Ao integrar o Upshotly ao Azure AD, você poderá:

* Controlar quem tem acesso ao Upshotly no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Upshotly com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Upshotly.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Upshotly é compatível com SSO iniciado por **SP e IDP**

## <a name="adding-upshotly-from-the-gallery"></a>Adicionar o Upshotly da galeria

Para configurar a integração do Upshotly ao Azure AD, você precisa adicionar o Upshotly da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Upshotly** na caixa de pesquisa.
1. Selecione **Upshotly** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-upshotly"></a>Configurar e testar o SSO do Azure AD para o Upshotly

Configure e teste o SSO do Azure AD com o Upshotly usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Upshotly.

Para configurar e testar o SSO do Azure AD com o Upshotly, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Upshotly](#configure-upshotly-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Upshotly](#create-upshotly-test-user)** – para ter um equivalente de B.Fernandes no Upshotly que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Upshotly**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/lápis da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, se desejar configurar o aplicativo no modo iniciado por **IDP**, o aplicativo será pré-configurado e as URLs necessárias já serão preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://app.upshotly.com/api/sso/login/<companyID>`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esses valores com a URL de Logon real. Você obterá o valor **companyID**, que será explicado posteriormente no tutorial. Entre em contato com a [equipe de suporte ao cliente do Upshotly](mailto:support@upshotly.com) para obter consultas. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Upshotly**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Upshotly.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Upshotly**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-upshotly-sso"></a>Configurar o SSO do Upshotly

1. Para automatizar a configuração no Upshotly, é necessário instalar a **Extensão do navegador de Conexão Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, ao clicar em **Configurar o Upshotly** você será direcionado ao aplicativo do Upshotly. De lá, forneça as credenciais de administrador para entrar no Upshotly. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 4.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Upshotly manualmente, em outra janela do navegador da Web, entre no site da empresa do Upshotly como administrador.

1. Clique no **Perfil do Usuário** e navegue até **Administrador > SSO** e siga estas etapas:

    ![Configuração do Upshotly](./media/upshotly-tutorial/config1.png)

    a. Copie o valor **ID da Empresa** e use esse valor **ID da Empresa** para substituir o valor **ID da Empresa** presente na **URL de Logon** na seção **Configuração Básica de SAML** no portal do Azure.

    b. Abra o **XML de metadados de federação** baixado do portal do Azure no Bloco de Notas, copie o conteúdo do XML de metadados e cole-o na caixa de texto **Metadados XML**.

### <a name="create-upshotly-test-user"></a>Criar usuário de teste do Upshotly

Nesta seção, você criará uma usuária chamada B.Fernandes no Upshotly Edge Cloud. Trabalhe com a [equipe de suporte ao cliente do Upshotly](mailto:support@upshotly.com) para adicionar os usuários na plataforma do Upshotly Edge Cloud. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Upshotly, na qual poderá iniciar o fluxo de logon.

* Acesse diretamente a URL de Logon do Upshotly, na qual você iniciará o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no Upshotly para o qual configurou o SSO

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Upshotly, em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Upshotly para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Upshotly, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
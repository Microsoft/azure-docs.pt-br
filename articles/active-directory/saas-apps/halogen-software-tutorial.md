---
title: 'Tutorial: Integração do Azure Active Directory ao Saba TalentSpace | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: edb94e8e1b4e814bfac4a1a2a90c5ec71bc48c77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652988"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Saba TalentSpace

Neste tutorial, você aprenderá a integrar o Saba TalentSpace ao Azure AD (Azure Active Directory). Ao integrar o Saba TalentSpace ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Saba TalentSpace.
* Permitir que os usuários sejam conectados automaticamente ao Saba TalentSpace com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Saba TalentSpace.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Saba TalentSpace dá suporte ao SSO iniciado por **SP**

## <a name="add-saba-talentspace-from-the-gallery"></a>Adicionar Saba TalentSpace da galeria

Para configurar a integração do Saba TalentSpace ao Azure AD, você precisará adicionar o Saba TalentSpace por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Saba TalentSpace** na caixa de pesquisa.
1. Selecione **Saba TalentSpace** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-saba-talentspace"></a>Configurar e testar o SSO do Azure AD para o Saba TalentSpace

Configure e teste o SSO do Azure AD com o Saba TalentSpace usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Saba TalentSpace.

Para configurar e testar o SSO do Azure AD com o Saba TalentSpace, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Saba TalentSpace](#configure-saba-talentspace-sso)** : para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Saba TalentSpace](#create-saba-talentspace-test-user)** : para ter um equivalente de B.Fernandes no Saba TalentSpace que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Saba TalentSpace**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite a URL usando o seguinte padrão: `https://global.hgncloud.com/[companyname]/saml/login`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL usando o seguinte padrão: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. Na caixa de texto **URL de Resposta (URL do Serviço do Consumidor de Declaração)** digite a URL usando o seguinte padrão: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Saba TalentSpace](https://support.saba.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Saba TalentSpace**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Saba TalentSpace.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Saba TalentSpace**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-saba-talentspace-sso"></a>Configurar o SSO do Saba TalentSpace

1. Em outra janela do navegador, faça logon no aplicativo **Saba TalentSpace** como administrador.

2. Clique na guia **Opções** .
  
    ![Captura de tela que mostra a home page "saba TalentSpace" com a guia "Opções" selecionada.](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. No painel de navegação esquerdo, clique em **Configuração do SAML**.
  
    ![Captura de tela que mostra o painel de navegação esquerdo "Interface do Usuário" com a opção "Configuração do SAML" selecionada.](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Na página **Configuração do SAML** , realize as seguintes etapas:

    ![Captura de tela que mostra a página "Configuração do SAML" com as opções "Configurações" realçadas.](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. Como **Identificador exclusivo**, selecione **NameID**.

    b. Em **Identificador exclusivo mapeia para**, selecione **Nome de usuário**.
  
    c. Para carregar o arquivo de metadados baixado, clique em **Procurar** para selecionar o arquivo e clique em **Carregar arquivo**.

    d. Para testar a configuração, clique em **Executar Teste**.

    > [!NOTE]
    > Você precisa esperar pela mensagem "*O teste de SAML foi concluído. Feche esta janela*". Feche a janela do navegador aberta. A caixa de seleção **Habilitar SAML** só será habilitada se o teste for concluído.

    e. Selecione **Habilitar SAML**.

    f. Clique em **Salvar Alterações**.

### <a name="create-saba-talentspace-test-user"></a>Criar um usuário de teste do Saba TalentSpace

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Saba TalentSpace.

**Para criar um usuário chamado Brenda Fernandes no Saba TalentSpace, execute as seguintes etapas:**

1. Faça logon em seu aplicativo **Saba TalentSpace** como administrador.

2. Clique na guia **Central do Usuário** e clique em **Criar Usuário**.

    ![Captura de tela que mostra a guia "Central do Usuário" e a opção "Criar Usuário" selecionada.](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Na página do diálogo **Novo Usuário** , realize as seguintes etapas:

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário, como **B**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **Fernandes**.

    c. Na caixa de texto **Nome de usuário**, digite **B.Fernandes**, o nome de usuário como mostrado no portal do Azure.

    d. Na caixa de texto **Senha**, digite uma senha para B.Fernandes.

    e. Clique em **Save** (Salvar).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso vai redirecionar você à URL de Logon do Saba TalentSpace na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Saba TalentSpace diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Saba TalentSpace em Meus Aplicativos, você deverá ser conectado automaticamente ao Saba TalentSpace para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

 Depois de configurar o Saba TalentSpace, você poderá impor controles de sessão, que fornecem proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
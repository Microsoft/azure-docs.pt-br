---
title: 'Tutorial: integração do Azure Active Directory com o Citrix ShareFile | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 03f2ec7aef1faadcb72d6c7a5a058c7d06596539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729661"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: integração do Azure Active Directory ao Citrix ShareFile

Nesse tutorial, você aprenderá a integrar o Citrix ShareFile ao Azure AD (Azure Active Directory).
A integração do Citrix ShareFile ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Citrix ShareFile.
* Você pode permitir que os usuários sejam conectados automaticamente ao Citrix ShareFile (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Citrix ShareFile, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso não tenha um ambiente do Azure AD, obtenha uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura do Citrix ShareFile habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Citrix ShareFile dá suporte ao SSO iniciado por **SP**

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adicionar o Citrix ShareFile da galeria

Para configurar a integração do Citrix ShareFile com o Azure AD, você precisa adicionar o Citrix ShareFile, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Citrix ShareFile** na caixa de pesquisa.
1. Selecione **Citrix ShareFile** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>Configurar e testar o SSO do Azure AD para o Citrix ShareFile

Nesta seção, você configurará e testará o logon único do Azure AD com o Citrix ShareFile, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Citrix ShareFile.

Execute as seguintes etapas para configurar e testar o logon único do Azure AD com o Citrix ShareFile:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Citrix ShareFile](#configure-citrix-sharefile-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Citrix ShareFile](#create-citrix-sharefile-test-user)** – para ter um equivalente de Brenda Fernandes no Citrix ShareFile que esteja vinculado à representação de usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Citrix ShareFile**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<tenant-name>.sharefile.com/saml/login`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: 
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Citrix ShareFile**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Citrix ShareFile.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Citrix ShareFile**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-citrix-sharefile-sso"></a>Configurar o SSO do Citrix ShareFile

1. Para automatizar a configuração no **Citrix ShareFile**, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, um clique em **Configurar o Citrix ShareFile** direcionará você ao aplicativo Citrix ShareFile. Nele, forneça as credenciais de administrador para entrar no Citrix ShareFile. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Citrix ShareFile manualmente, em outra janela do navegador da Web, entre em seu site da empresa do Citrix ShareFile como administrador.

1. No **Painel**, clique em **Configurações** e selecione **Configurações de Administrador**.

    ![Administração](./media/sharefile-tutorial/settings.png)

1. Em Configurações de Administrador, acesse **Segurança** -> **Logon e Política de Segurança**.
   
    ![Administração de Conta](./media/sharefile-tutorial/settings-security.png "Administração de Conta")

1. Na página da caixa de diálogo **Configuração de Logon Único/ SAML 2.0** em **Configurações Básicas**, execute as seguintes etapas:
   
    ![Logon Único](./media/sharefile-tutorial/saml-configuration.png "Logon único")
   
    a. Selecione **SIM** em **Habilitar SAML**.

    b. Copie o valor da **ID da Entidade/Emissor do ShareFile** e cole-o na caixa **URL do Identificador** da caixa de diálogo **Configuração Básica do SAML** no portal do Azure.
    
    c. Na caixa de texto **Emissor IDP/ID da Entidade**, cole o valor de **Identificador do Azure AD** copiado no portal do Azure.

    d. Clique em **Alterar** ao lado do campo **Certificado X.509** e carregue o certificado baixado no portal Azure.
    
    e. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.
    
    f. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado no portal do Azure.

    g. Em **Configurações Opcionais**, escolha **Contexto de Autenticação Iniciado pelo SP** como **Nome de Usuário e Senha** e **Exatos**.

5. Clique em **Salvar**.

## <a name="create-citrix-sharefile-test-user"></a>Criar um usuário de teste do Citrix ShareFile

1. Faça logon no seu locatário do **Citrix ShareFile**.

2. Clique em **Pessoas** -> **Gerenciar Página Inicial dos Usuários** -> **Criar Usuários** -> **Criar Funcionário**.
   
    ![Criar Funcionário](./media/sharefile-tutorial/create-user.png "Criar Funcionário")

3. Na seção **Informações Básicas**, execute as etapas abaixo:
   
    ![Informações Básicas](./media/sharefile-tutorial/user-form.png "Informações Básicas")
   
    a. Na caixa de texto **Nome**, digite o **nome** do usuário como **Brenda**.
   
    b.  Na caixa de texto **Sobrenome**, digite o **sobrenome** do usuário como **Fernandes**.
   
    c. Na caixa de texto **Endereço de Email**, digite o endereço de email de Brenda Fernandes como **brendafernandes\@contoso.com**.

4. Clique em **Adicionar Usuário**.
  
    >[!NOTE]
    >O titular da conta do Azure AD receberá um email e seguirá um link para confirmar sua conta antes de se tornar ativo. Você pode usar quaisquer outras ferramentas de criação de conta de usuário do Citrix ShareFile ou APIs fornecidas pelo Citrix ShareFile para provisionar contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado para a URL de Entrada do Citrix ShareFile, onde poderá iniciar o fluxo de logon.

* Acesse a URL de Entrada do Citrix ShareFile diretamente e inicie o fluxo de logon desse local.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Citrix ShareFile em Meus Aplicativos, você será redirecionado para a URL de Entrada do Citrix ShareFile. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Citrix ShareFile, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do Azure Active Directory com o ZPA (Zscaler Private Access) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ZPA (Zscaler Private Access).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 58e2a19f2d57eafc7d2967141d584dc7a22fe76c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955649"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Tutorial: Integração do ZPA (Zscaler Private Access) ao Azure AD (Azure Active Directory)

Neste tutorial, você aprenderá a integrar o ZPA (Zscaler Private Access) ao Azure AD (Azure Active Directory). Quando você integra o ZPA (Zscaler Private Access) ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao ZPA (Zscaler Private Access).
* Permitir que os usuários sejam conectados automaticamente ao ZPA (Zscaler Private Access) com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do ZPA (Zscaler Private Access).

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* O ZPA (Zscaler Private Access) dá suporte ao SSO iniciado por **SP**.

## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Adicionar o ZPA (Zscaler Private Access) da galeria

Para configurar a integração do ZPA (Zscaler Private Access) ao Azure AD, você precisará adicionar o ZPA (Zscaler Private Access) da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ZPA (Zscaler Private Access)** na caixa de pesquisa.
1. Escolha **ZPA (Zscaler Private Access)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o ZPA (Zscaler Private Access) com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ZPA (Zscaler Private Access).

Para configurar e testar o SSO do Azure AD com o ZPA (Zscaler Private Access), execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configure o SSO do ZPA (Zscaler Private Access)](#configure-zscaler-private-access-zpa-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Crie um usuário de teste do ZPA (Zscaler Private Access)](#create-zscaler-private-access-zpa-test-user)** – para ter um equivalente de B.Fernandes no ZPA (Zscaler Private Access) que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **ZAPA (Zscaler Private Access)** , localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > O valor da **URL de logon** não é real. Atualize o valor com a URL de Entrada real. Contate a [equipe de suporte ao cliente do ZPA (Zscaler Private Access)](https://help.zscaler.com/zpa-submit-ticket) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, localize **XML de Metadados de Federação** e escolha **Download** para fazer o download do certificado e salve-o em seu computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o ZPA (Zscaler Private Access)** , copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso ao ZPA (Zscaler Private Access).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ZPA (Zscaler Private Access)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-zscaler-private-access-zpa-sso"></a>Configurar o SSO do ZPA (Zscaler Private Access)

1. Para automatizar a configuração no ZPA (Zscaler Private Access), é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Configurar o ZPA (Zscaler Private Access)** direcionará você ao aplicativo ZPA (Zscaler Private Access). Lá, forneça as credenciais de administrador para entrar no ZPA (Zscaler Private Access). A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o ZPA (Zscaler Private Access) manualmente, abra uma nova janela do navegador da Web, entre no site empresarial do ZPA (Zscaler Private Access) como administrador e execute as seguintes etapas:

4. No menu à esquerda, clique em **Administração**, navegue até a seção **AUTENTICAÇÃO** e clique em **Configuração de IdP**.

    ![Administração do Administrador do Zscaler Private Access](./media/zscalerprivateaccess-tutorial/administration.png)

5. No canto superior direito, clique em **Adicionar Configuração de IdP**. 

    ![idp do Administrador do Zscaler Private Access](./media/zscalerprivateaccess-tutorial/metadata.png)

6. Na página **Adicionar Configuração de IdP**, execute as seguintes etapas:
 
    ![seleção do Administrador do Zscaler Private Access](./media/zscalerprivateaccess-tutorial/select.png)

    a. Clique em **Selecionar Arquivo** para carregar o arquivo de metadados baixado do Azure AD no campo **Upload de Arquivo de Metadados de IdP**.

    b. Ele lê os **metadados de IdP** do Azure AD e popula todas as informações dos campos, conforme mostrado abaixo.

    ![configuração do Administrador do Zscaler Private Access](./media/zscalerprivateaccess-tutorial/configure.png)

    c. Selecione seu domínio do campo **Domínios**.
    
    d. Clique em **Save** (Salvar).

### <a name="create-zscaler-private-access-zpa-test-user"></a>Criar um usuário de teste do ZPA (Zscaler Private Access)

Nesta seção, você deve criar uma usuária chamada Brenda Fernandes no ZPA (Zscaler Private Access). Trabalhe com [equipe de suporte do ZPA (Zscaler Private Access)](https://help.zscaler.com/zpa-submit-ticket) para adicionar os usuários na plataforma ZPA (Zscaler Private Access).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de logon do ZPA (Zscaler Private Access), na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de logon do ZPA (Zscaler Private Access) e inicie o fluxo de logon.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do ZPA (Zscaler Private Access) em Meus Aplicativos, você será redirecionado à URL de Logon do ZPA (Zscaler Private Access). Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o ZPA (Zscaler Private Access), você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Samsung Knox and Business Services   Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Samsung Knox and Business Services.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 330c02f15c0818f0a5c69088757c92a91a523589
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952636"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Samsung Knox and Business Services

Neste tutorial, você aprenderá a integrar o Samsung Knox and Business Services ao Azure AD (Azure Active Directory). Quando você integrar o Samsung Knox and Business Services ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Samsung Knox and Business Services.
* Permitir que os usuários sejam conectados automaticamente ao Samsung Knox and Business Services com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma conta do Samsung Knox.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Samsung Knox and Business Services é compatível com o SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>Adicionar o Samsung Knox and Business Services da galeria

Para configurar a integração do Samsung Knox and Business Services ao Azure AD, você precisa adicionar o Samsung Knox and Business Services da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Samsung Knox and Business Services** na caixa de pesquisa.
1. Selecione **Samsung Knox and Business Services** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Configurar e testar o SSO do Azure AD para o Samsung Knox and Business Services

Configure e teste o SSO do Azure AD com o Samsung Knox and Business Services usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em [SamsungKnox.com](https://samsungknox.com/).

Para configurar e testar o SSO do Azure AD com o Samsung Knox and Business Services, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Samsung Knox and Business Services](#configure-samsung-knox-and-business-services-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Samsung Knox and Business Services](#create-samsung-knox-and-business-services-test-user)** – para ter um equivalente de B.Fernandes no Samsung Knox and Business Services que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Samsung Knox and Business Services**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    * Na caixa de texto **URL de Logon**, digite a URL: `https://www.samsungknox.com`
    * Na caixa de texto **URL de Resposta (URL do serviço do consumidor de declaração)** , digite a URL: `https://central.samsungknox.com/ams/ad/saml/acs`
    
    ![Valores da configuração básica do SAML](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Samsung Knox and Business Services.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Samsung Knox and Business Services**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-samsung-knox-and-business-services-sso"></a>Configurar o SSO do Samsung Knox and Business Services

1. Em uma outra janela do navegador da Web, entre em [SamsungKnox.com](https://samsungknox.com/) como administrador.

1. Clique no **Avatar** no canto superior direito.

    ![Avatar do Samsung Knox](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. Na barra lateral esquerda, clique em **CONFIGURAÇÕES DO ACTIVE DIRECTORY** e execute as etapas a seguir.

    ![CONFIGURAÇÕES DO ACTIVE DIRECTORY](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. Na caixa de texto **Identificador (ID da entidade)** , cole o valor de **Identificador** que você inseriu no portal do Azure.

    b. Na caixa de texto **URL de metadados de federação do aplicativo**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do portal do Azure.

    c. Clique em **CONECTAR-SE AO SSO DO AD**.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Configurar um usuário de teste do Samsung Knox and Business Services

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Samsung Knox and Business Services. Consulte os guias de administração [Configurar o Knox](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm) ou [Inscrição no Knox Mobile](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm) para obter instruções de como convidar um subadministrador ou um usuário de teste para sua organização do Samsung Knox. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para [SamsungKnox.com](https://samsungknox.com/), onde você poderá iniciar o fluxo de logon. 

* Acesse [SamsungKnox.com](https://samsungknox.com/) diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Samsung Knox and Business Services em Meus Aplicativos, isso vai redirecionar você para [SamsungKnox.com](https://samsungknox.com/). Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Samsung Knox and Business Services, você poderá impor controles de sessão, que fornecem proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
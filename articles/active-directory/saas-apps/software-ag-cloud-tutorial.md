---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Software AG Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Software AG Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: c2a2446da0e1ccf1b3dab88fe898b179291ddaba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726080"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Software AG Cloud

Neste tutorial, você aprenderá a integrar o Software AG Cloud ao Azure Active Directory (Azure AD). Com a integração do Software AG Cloud ao Azure AD, é possível:

* Controlar, no Azure AD, quem tem acesso ao Software AG Cloud.
* Permitir que os usuários sejam conectados automaticamente ao Software AG Cloud com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Software AG Cloud habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Software AG Cloud é compatível com SSO iniciado por **SP**
* O Software AG Cloud é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Como adicionar o Software AG Cloud por meio da galeria

Para configurar a integração do Software AG Cloud com o Azure AD, você precisa adicionar o Software AG Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Software AG Cloud** na caixa de pesquisa.
1. Selecione **Software AG Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Configurar e testar o SSO do Azure AD para o Software AG Cloud

Configure e teste o SSO do Azure AD com o Software AG Cloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Software AG Cloud.

Para configurar e testar o SSO do Azure AD com o Software AG Cloud, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Software AG Cloud](#configure-software-ag-cloud-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Software AG Cloud](#create-software-ag-cloud-test-user)** – para ter um equivalente de B.Fernandes no Software AG Cloud que esteja vinculado à representação da usuária no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Software AG Cloud**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: 

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    1. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

        > [!NOTE]
        > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Software AG Cloud](mailto:support@softwareag.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Software AG Cloud**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Software AG Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Software AG Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-software-ag-cloud-sso"></a>Configurar o SSO do Software AG Cloud

1. Em outra janela do navegador da Web, entre no site do Software AG Cloud como administrador.

1.  Clique em **Administração**

    ![Como configurar a Administração do Software AG Cloud](./media/software-ag-cloud-tutorial/admin.png)

1. Acesse **Logon único > Adicionar provedor de identidade**

    ![Como configurar o provedor de identidade do Software AG Cloud](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Execute as etapas abaixo na página a seguir.

    ![Como configurar as etapas a serem seguidas do Software AG Cloud](./media/software-ag-cloud-tutorial/saml-1.png)

    a. Na caixa de texto **Nome de exibição do provedor de identidade**, digite qualquer nome válido, como `azure ad`.

    b. Na caixa de texto **Identificador exclusivo do provedor de identidade para uso no URI de redirecionamento do Software AG Cloud**, insira um nome exclusivo para o Provedor de Identidade. O campo **URI de redirecionamento do Software AG Cloud** será atualizado e populado com o URI. Copie esse URI e use-o para configurar a **ID de Entidade** e outras informações no portal do Azure de acordo com os padrões definidos.

    c. Importe o arquivo **XML de Metadados de Federação** em **Configuração do provedor de identidade** e clique em **Avançar**.

    d. Vá para a página **Configuração** e preencha os campos conforme necessário.

### <a name="create-software-ag-cloud-test-user"></a>Criar um usuário de teste do Software AG Cloud

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Software AG Cloud. O Software AG Cloud é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se nenhum usuário existir no Software AG Cloud, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com a opção a seguir. 

Pressupondo que o Microsoft Azure esteja configurado como um provedor no Software AG Cloud, navegue até `www.softwareag.cloud` e clique no botão Logon e insira o nome do ambiente. Na próxima tela, clique no link "Fazer logon com <IDP NAME>" e insira as credenciais. Depois de autenticado, você será conectado e direcionado à home page do Software AG Cloud.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Software AG Cloud, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
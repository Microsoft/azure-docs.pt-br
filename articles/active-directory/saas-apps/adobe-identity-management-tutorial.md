---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Adobe Identity Management | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Identity Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726361"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Adobe Identity Management

Neste tutorial, você aprenderá a integrar o Adobe Identity Management ao Azure AD (Azure Active Directory). Com a integração do Adobe Identity Management ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Adobe Identity Management.
* Permitir que os usuários sejam conectados automaticamente ao Adobe Identity Management usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Adobe Identity Management habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Adobe Identity Management dá suporte ao SSO iniciado por **SP**

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Adicionando o Adobe Identity Management da galeria

Para configurar a integração do Adobe Identity Management ao Azure AD, você precisará adicionar o Adobe Identity Management da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Adobe Identity Management** na caixa de pesquisa.
1. Selecione **Adobe Identity Management** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Configurar e testar o SSO do Azure AD para o Adobe Identity Management

Configure e teste o SSO do Azure AD com o Adobe Identity Management usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adobe Identity Management.

Para configurar e testar o SSO do Azure AD com o Adobe Identity Management, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar SSO do Adobe Identity Management](#configure-adobe-identity-management-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Adobe Identity Management](#create-adobe-identity-management-test-user)** – para ter um equivalente de Brenda Fernandes no Adobe Identity Management que esteja vinculado à representação da usuária no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Adobe Identity Management**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://adobe.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Entre em contato com a [equipe de suporte ao cliente do Adobe Identity Management](mailto:identity@adobe.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Adobe Identity Management**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure dando a ela acesso ao Adobe Identity Management.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Adobe Identity Management**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-adobe-identity-management-sso"></a>Configurar o SSO do Adobe Identity Management

1. Para automatizar a configuração no Adobe Identity Management, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Adobe Identity Management** para acessar o aplicativo Adobe Identity Management. Nele, forneça as credenciais de administrador para entrar no Adobe Identity Management. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 8.

    ![Configuração da instalação](common/setup-sso.png)

3. Caso deseje configurar o Adobe Identity Management manualmente, em outra janela do navegador da Web, entre no seu site de empresa do Adobe Identity Management como administrador.

4. Acesse a guia **Configurações** e clique em **Criar Diretório**.

    ![Configurações do Adobe Identity Management](./media/adobe-identity-management-tutorial/settings.png)

5. Forneça o nome do diretório na caixa de texto, selecione **ID Federada** e clique em **Avançar**.

    ![Criar Diretório no Adobe Identity Management](./media/adobe-identity-management-tutorial/create-directory.png)

6. Selecione **Outros Provedores SAML** e clique em **Avançar**.
 
    ![Provedores SAML do Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Clique em **Selecionar** para carregar o arquivo **XML de Metadados** que você baixou do portal do Azure.

    ![Configuração do SAML do Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Clique em **Concluído**.

### <a name="create-adobe-identity-management-test-user"></a>Criar um usuário de teste do Adobe Identity Management

1. Acesse a guia **Usuários** e clique em **Adicionar Usuário.**

    ![Adicionar Usuário no Adobe Identity Management](./media/adobe-identity-management-tutorial/add-user.png)

2. Na caixa de texto **Inserir o endereço de email do usuário**, forneça o **endereço de email**.

    ![Salvar Usuário no Adobe Identity Management](./media/adobe-identity-management-tutorial/save-user.png)

3. Clique em **Save** (Salvar).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Adobe Identity Management, na qual você poderá iniciar o fluxo de logon.

* Acesse diretamente a URL de Logon do Adobe Identity Management e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Adobe Identity Management em Meus Aplicativos, você será redirecionado para a URL de Logon do Adobe Identity Management. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Adobe Identity Management, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
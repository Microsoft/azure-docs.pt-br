---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao HowNow WebApp SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HowNow WebApp SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: jeedes
ms.openlocfilehash: 8d5881d838c4fe952206afb827fd60ed98dbba86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96178255"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hownow-webapp-sso"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao HowNow WebApp SSO

Neste tutorial, você aprenderá a integrar o HowNow WebApp SSO ao Azure AD (Azure Active Directory). Quando você integra o HowNow WebApp SSO ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao HowNow WebApp SSO.
* Permitir que os usuários sejam conectados automaticamente ao HowNow WebApp SSO com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do HowNow WebApp SSO habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O HowNow WebApp SSO é compatível com o SSO iniciado por **SP**

* O HowNow WebApp SSO é compatível com o provisionamento de usuário **just-in-time**

## <a name="adding-hownow-webapp-sso-from-the-gallery"></a>Adicionar o HowNow WebApp SSO da galeria

Para configurar a integração do HowNow WebApp SSO ao Azure AD, você precisa adicionar o HowNow WebApp SSO da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **HowNow WebApp SSO** na caixa de pesquisa.
1. Selecione **HowNow WebApp SSO** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-hownow-webapp-sso"></a>Configurar e testar o SSO do Azure AD para o HowNow WebApp SSO

Configure e teste o SSO do Azure AD com o HowNow WebApp SSO usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HowNow WebApp SSO.

Para configurar e testar o SSO do Azure AD com o HowNow WebApp SSO, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do HowNow WebApp SSO](#configure-hownow-webapp-sso-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar usuário de teste do HowNow WebApp SSO](#create-hownow-webapp-sso-test-user)** – para que haja um equivalente de B.Fernandes no HowNow WebApp SSO vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **HowNow WebApp SSO**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<CUSTOMER_NAME>.hownow.app/users/saml/sign_in`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<CUSTOMER_NAME>.hownow.app/users/saml/metadata`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CUSTOMER_NAME>.hownow.app/users/saml/auth`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Entre em contato com a [equipe de suporte ao Cliente do HowNow WebApp SSO](mailto:support@gethownow.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o **Valor da Impressão Digital** e salve-o em seu computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o HowNow WebApp SSO**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao HowNow WebApp SSO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **HowNow WebApp SSO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-hownow-webapp-sso-sso"></a>Configurar o SSO do HowNow WebApp SSO

Para configurar o logon único no lado do **HowNow WebApp SSO**, é necessário enviar o **Valor da Impressão Digital** e as URLs apropriadas copiadas do portal do Azure para [a equipe de suporte do HowNow WebApp SSO](mailto:support@gethownow.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-hownow-webapp-sso-test-user"></a>Criar usuário de teste do HowNow WebApp SSO

Nesta seção, uma usuária chamada Brenda Fernandes será criada no HowNow WebApp SSO. O HowNow WebApp SSO é compatível com o provisionamento de usuário just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se o usuário ainda não existir no HowNow WebApp SSO, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do HowNow WebApp SSO, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do HowNow WebApp SSO, na qual você iniciará o fluxo de logon.

* Use o Painel de Acesso da Microsoft. Quando você clicar no bloco do HowNow WebApp SSO no Painel de Acesso, isso redirecionará você à URL de Logon do HowNow WebApp SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o HowNow WebApp SSO, você poderá impor o controle de sessão, que protege contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
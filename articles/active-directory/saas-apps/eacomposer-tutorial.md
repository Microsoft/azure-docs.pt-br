---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EAComposer | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EAComposer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: ab7263cd2b4988a83d8eae67264705dd09f28456
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180941"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eacomposer"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EAComposer

Neste tutorial, você aprenderá como integrar o EAComposer ao Azure AD (Azure Active Directory). Ao integrar o EAComposer ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao EAComposer.
* Permitir que seus usuários sejam automaticamente conectados ao EAComposer com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do EAComposer.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EAComposer é compatível com o SSO iniciado por **SP**
* O EAComposer é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-eacomposer-from-the-gallery"></a>Como adicionar o EAComposer da galeria

Para configurar a integração do EAComposer ao Azure AD, você precisará adicionar o EAComposer da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **EAComposer** na caixa de pesquisa.
1. Selecione **EAComposer** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-eacomposer"></a>Configurar e testar o SSO do Azure AD para o EAComposer

Configure e teste o SSO do Azure AD com o EAComposer usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do EAComposer.

Para configurar e testar o SSO do Azure AD com o EAComposer, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do EAComposer](#configure-eacomposer-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste no EAComposer](#create-eacomposer-test-user)** – para ter um equivalente de B.Fernandes no EAComposer que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **EAComposer**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite a URL: `https://www.eacomposer.com/solution/login.aspx`

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o **Valor da Impressão Digital** e salve-o em seu computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o EAComposer**, copie as URLs apropriadas de acordo com seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao EAComposer.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **EAComposer**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-eacomposer-sso"></a>Configurar o SSO do EAComposer

Para configurar o logon único no lado do **EAComposer**, é necessário enviar o **Valor da Impressão Digital** e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do EAComposer](mailto:support@eacomposer.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-eacomposer-test-user"></a>Criar um usuário de teste do EAComposer

Nesta seção, um usuário chamado Brenda Fernandes é criado no EAComposer. O EAComposer é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no EAComposer, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do EAComposer, na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de Logon do EAComposer diretamente e inicie o fluxo de logon de lá.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco do EAComposer no Painel de Acesso, você será redirecionado à URL de Logon do EAComposer. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o EAComposer, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
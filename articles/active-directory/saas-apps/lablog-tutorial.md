---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao LabLog | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LabLog.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: d52ea8d6af84568f9dd458aefdecd36d3b36457d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727332"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lablog"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao LabLog

Neste tutorial, você aprenderá a integrar o LabLog ao Azure AD (Azure Active Directory). Ao integrar o LabLog ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao LabLog.
* Permitir que os usuários sejam conectados automaticamente ao LabLog com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do LabLog habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O LabLog é compatível com o SSO iniciado por **SP**

* O LabLog dá suporte ao provisionamento de usuário **Just-In-Time**


## <a name="adding-lablog-from-the-gallery"></a>Como adicionar o LabLog da galeria

Para configurar a integração do LabLog ao Azure AD, você precisará adicionar o LabLog por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **LabLog** na caixa de pesquisa.
1. Selecione **LabLog** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-lablog"></a>Configurar e testar o SSO do Azure AD para o LabLog

Configure e teste o SSO do Azure AD com o LabLog usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LabLog.

Para configurar e testar o SSO do Azure AD com o LabLog, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do LabLog](#configure-lablog-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do LabLog](#create-lablog-test-user)** – para ter um equivalente de B. Fernandes no LabLog que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **LabLog**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<CUSTOMER_SUBDOMAIN>.labnotebook.app/lablog/login/sso/`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do LabLog](mailto:support@labnotebook.app) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o LabLog**, copie as URLs corretas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao LabLog.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **LabLog**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-lablog-sso"></a>Configurar o SSO do LabLog

1. Faça logon no site do LabLog como administrador.

1. Clique no ícone **Logon Único** no menu à esquerda.

1. Execute as etapas abaixo na página a seguir.

    ![Configuração do LabLog](./media/lablog-tutorial/single-sign-on.png)

    a. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    b. Na caixa de texto **URL de Logon por SSO SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de Notas e cole o conteúdo na caixa de texto **Certificado Público**.

    d. Clique em **SALVAR**.


### <a name="create-lablog-test-user"></a>Criar usuário de teste do LabLog

Nesta seção, um usuário chamado Brenda Fernandes é criado no LabLog. O LabLog dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no LabLog, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon LabLog, na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do LabLog diretamente e inicie dela o fluxo de logon.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco LabLog em Meus Aplicativos, isso redirecionará para a URL de logon do LabLog. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o LabLog, você poderá impor o controle de sessão, que protege contra o vazamento e a infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
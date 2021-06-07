---
title: 'Tutorial: Integração do Azure Active Directory ao AppDynamics | Microsoft Azure'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AppDynamics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: b83ec6b63d908483801a28ba89227c8431401424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589252"
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Tutorial: Integração do Active Directory do Azure ao AppDynamics

Neste tutorial, você aprenderá como integrar o AppDynamics ao Azure AD (Azure Active Directory). Ao integrar o AppDynamics ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao AppDynamics.
* Habilitar os usuários para serem conectados automaticamente ao AppDynamics com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do AppDynamics.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O AppDynamics é compatível com o SSO iniciado por **SP**.

* O AppDynamics é compatível com o provisionamento de usuário **Just In Time**.

## <a name="add-appdynamics-from-the-gallery"></a>Adicionar o AppDynamics por meio da galeria

Para configurar a integração do AppDynamics ao Azure AD, você precisa adicionar o AppDynamics à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AppDynamics** na caixa de pesquisa.
1. Selecione **AppDynamics** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-appdynamics"></a>Configurar e testar o SSO do Azure AD para o AppDynamics

Configurar e testar o SSO do Azure AD com o AppDynamics usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do AppDynamics.

Para configurar e testar o SSO do Azure AD com o AppDynamics, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do AppDynamics](#configure-appdynamics-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do AppDynamics](#create-appdynamics-test-user)** – para ter um equivalente de B.Fernandes no AppDynamics que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **AppDynamics**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.saas.appdynamics.com?accountName=<companyname>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do AppDynamics](https://www.appdynamics.com/support/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o AppDynamics**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao AppDynamics.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AppDynamics**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name=&quot;configure-appdynamics-sso&quot;></a>Configurar o SSO do AppDynamics

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do AppDynamics como administrador.

1. Na barra de ferramentas na parte superior, clique em **Configurações** e, em seguida, clique em **Administração**.

    ![Administração](./media/appdynamics-tutorial/settings.png &quot;Administração")

1. Clique na guia **Provedor de Autenticação**.

    ![Provedor de Autenticação](./media/appdynamics-tutorial/authentication.png "Provedor de Autenticação")

1. Na seção **Provedor de Autenticação**, execute as etapas a seguir:

    ![Configuração de SAML](./media/appdynamics-tutorial/configuration.png "Configuração de SAML")

   a. Como **Provedor de autenticação**, selecione **SAML**.

   b. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

   c. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

   d. Abra seu certificado codificado base 64 no bloco de notas, copie o conteúdo dele para sua área de transferência e cole-o na caixa de texto **Certificado**

   e. Clique em **Save** (Salvar).

### <a name="create-appdynamics-test-user"></a>Criar um usuário de teste do AppDynamics

Nesta seção, um usuário chamado B.Fernandes será criado no AppDynamics. O AppDynamics é compatível com o provisionamento de usuário Just-in-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no AppDynamics, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do AppDynamics, por meio da qual será possível iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do AppDynamics e inicie o fluxo de logon por meio dela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do AppDynamics em Meus Aplicativos, isso redirecionará você à URL de logon do AppDynamics. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o AppDynamics, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
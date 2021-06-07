---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Exium | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Exium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 01b3bbc0a4f0c7d53ed0a9ba2449cf5985b2ab86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608345"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Exium

Neste tutorial, você aprenderá a integrar o Exium ao Azure AD (Azure Active Directory). Ao integrar o Exium ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Exium.
* Permitir que os usuários entrem automaticamente no Exium com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Exium habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Exium dá suporte ao SSO iniciado por **SP**.

## <a name="adding-exium-from-the-gallery"></a>Como adicionar o Exium por meio da galeria

Para configurar a integração do Exium ao Azure AD, você precisará adicionar o Exium por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Exium** na caixa de pesquisa.
1. Escolha **Exium** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>Configurar e testar o SSO do Azure AD para o Exium

Configure e teste o SSO do Azure AD com o Exium por meio de um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Exium.

Para configurar e testar o SSO do Azure AD com o Exium, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Exium](#configure-exium-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Exium](#create-exium-test-user)** : para ter um equivalente de B.Fernandes no Exium que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Exium**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://subapi.exium.net/saml/<WORKSPACE_ID>/acs`

    c. Na caixa de texto **URL de Logon**, digite a URL: `https://service.exium.net/sign-in`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Exium](mailto:support@exium.net) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Exium.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Exium**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-exium-sso"></a>Configurar o SSO do Exium

1. Entre no seu site de empresa do Exium como administrador.

1. No **Console de Administração**, selecione o painel **Perfil da Empresa**.

    ![captura de tela do console de administração](./media/exium-tutorial/company-profile.png)

1. No **Perfil**, clique em **Configurações de SSO** e selecione **Editar** para editá-las.

1. Execute as etapas abaixo na seção **Configurações de SSO**.

    ![captura de tela de Configurações de SSO](./media/exium-tutorial/update.png)

    a. Selecione **Tipo de SSO** como **AzureAD** no menu suspenso.

    b. Cole o valor da **URL de Metadados de Federação do Aplicativo** no campo **URL de Metadados do IdP do SAML 2.0**.

    c. Copie o valor da **URL de SSO do SAML 2.0** e cole-o na caixa de texto **URL de Resposta** na seção **Configuração Básica do SAML** no portal do Azure.

    d. Copie o valor da **ID da Entidade do SP do SAML 2.0** e cole-o na caixa de texto **Identificador** na seção **Configuração Básica do SAML** no portal do Azure.  

    e. Clique em **Atualizar**.

### <a name="create-exium-test-user"></a>Criar um usuário de teste do Exium

1. Entre no seu site de empresa do Exium como administrador.

1. Acesse **Gerenciamento de Usuários -> Usuários** e clique em **Adicionar Usuário**.

    ![captura de tela de Criar um usuário de teste](./media/exium-tutorial/add-user.png)

1. Insira os campos obrigatórios na página a seguir e clique em **Salvar**.

    ![captura de tela dos campos de Criar um usuário de teste com o botão Salvar](./media/exium-tutorial/add-user-2.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Exium, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Exium e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Exium em Meus Aplicativos, você será redirecionado à URL de Logon do Exium. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Exium, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).



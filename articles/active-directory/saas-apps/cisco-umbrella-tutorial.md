---
title: 'Tutorial: Integração do Azure Active Directory ao Cisco Umbrella Admin SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Umbrella Admin SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 6073142b21a28f1242e0e6ec65ee2945a354b60b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592496"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Tutorial: Integração do Azure Active Directory ao Cisco Umbrella Admin SSO

Neste tutorial, você aprenderá a integrar o Cisco Umbrella Admin SSO ao Azure AD (Azure Active Directory). Ao integrar o Cisco Umbrella Admin SSO ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Cisco Umbrella Admin SSO.
* Permitir que os usuários entrem automaticamente no Cisco Umbrella Admin SSO com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Cisco Umbrella Admin SSO habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Cisco Umbrella Admin SSO dá suporte ao SSO iniciado por **SP e IdP**.

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Adicionar o Cisco Umbrella Admin SSO por meio da galeria

Para configurar a integração do Cisco Umbrella Admin SSO com o Azure AD, você precisa adicionar o Cisco Umbrella Admin SSO da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Cisco Umbrella Admin SSO** na caixa de pesquisa.
1. Selecione **Cisco Umbrella Admin SSO** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Configurar e testar o SSO do Azure AD para o Cisco Umbrella Admin SSO

Configure e teste o SSO do Azure AD com o Cisco Umbrella Admin SSO usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Cisco Umbrella Admin SSO.

Para configurar e testar o SSO do Azure AD com o Cisco Umbrella Admin SSO, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Cisco Umbrella Admin SSO](#configure-cisco-umbrella-admin-sso-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Cisco Umbrella Admin SSO](#create-cisco-umbrella-admin-sso-test-user)** : para ter um equivalente de B.Fernandes no Cisco Umbrella Admin SSO que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Cisco Umbrella Admin SSO**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    a. Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    b. Clique em **Definir URLs adicionais**.

    c. Na caixa de texto **URL de Logon**, digite a URL: `https://login.umbrella.com/sso`

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **XML de metadados** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar Cisco Umbrella Admin SSO**, copie a URL apropriada de acordo com seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Cisco Umbrella Admin SSO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Cisco Umbrella Admin SSO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Configurar SSO do Cisco Umbrella Admin SSO

1. Em outra janela do navegador, entre em seu site da empresa do Cisco Umbrella Admin SSO como administrador.

2. No lado esquerdo do menu, clique em **Admin**, navegue até **Autenticação** e, em seguida, clique em **SAML**.

    ![O administrador](./media/cisco-umbrella-tutorial/admin.png)

3. Escolha **Outros** e clique em **AVANÇAR**.

    ![O outro](./media/cisco-umbrella-tutorial/other.png)

4. Na página de **Metadados do Cisco Umbrella Admin SSO**, clique em **AVANÇAR**.

    ![Os metadados](./media/cisco-umbrella-tutorial/metadata.png)

5. Na guia **Carregar Metadados**, se você tiver configurado o SAML previamente, selecione a opção **Clique aqui para alterá-los** e siga as próximas etapas.

    ![Avançar](./media/cisco-umbrella-tutorial/next.png)

6. Na **Opção A: Carregar arquivo XML**, carregue o arquivo **XML de Metadados de Federação** que você baixou do portal do Azure. Depois de carregar os metadados, os valores abaixo são populados automaticamente; em seguida, clique em **Avançar**.

    ![Escolher o arquivo](./media/cisco-umbrella-tutorial/choose-file.png)

7. Na seção **Validar Configuração do SAML**, clique em **TESTE SUA CONFIGURAÇÃO DE SAML**.

    ![O teste](./media/cisco-umbrella-tutorial/test.png)

8. Clique em **SALVAR**.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Criar um usuário de teste do Cisco Umbrella Admin SSO

Para permitir que os usuários do Azure AD façam logon no Cisco Umbrella Admin SSO, eles devem ser provisionados no Cisco Umbrella Admin SSO.  
No caso do Cisco Umbrella Admin SSO, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Em outra janela do navegador, entre em seu site da empresa do Cisco Umbrella Admin SSO como administrador.

2. No lado esquerdo do menu, clique em **Admin** e navegue até **Contas**.

    ![A conta](./media/cisco-umbrella-tutorial/account.png)

3. Na página **Contas**, clique em **Adicionar** no lado superior direito da página e execute as seguintes etapas.

    ![O usuário](./media/cisco-umbrella-tutorial/create-user.png)

    a. No campo **Nome**, insira o nome, como **Brenda**.

    b. No campo **Sobrenome**, insira o sobrenome, como **Fernandes**.

    c. Em **Escolher Função de Administrador Delegado**, selecione sua função.

    d. No campo **Endereço de Email**, digite o endereço de email do usuário, como **brendafernandes\@contoso.com**.

    e. No campo **Senha**, insira sua senha.

    f. No campo **Confirmar Senha**, insira a senha novamente.

    g. Clique em **CRIAR**.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do Cisco Umbrella Admin SSO, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Cisco Umbrella Admin SSO e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no Cisco Umbrella Admin SSO, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Cisco Umbrella Admin SSO em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no Cisco Umbrella Admin SSO para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Cisco Umbrella Admin SSO, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
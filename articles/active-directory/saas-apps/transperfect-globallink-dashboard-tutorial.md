---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TransPerfect GlobalLink Dashboard   Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TransPerfect GlobalLink Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: ad720f5e625d2054a5c79b47c9659d4d10d251e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955067"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-transperfect-globallink-dashboard"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TransPerfect GlobalLink Dashboard

Neste tutorial, você aprenderá a integrar o TransPerfect GlobalLink Dashboard ao Azure AD (Azure Active Directory). Ao integrar o TransPerfect GlobalLink Dashboard ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao TransPerfect GlobalLink Dashboard.
* Permitir que os usuários sejam conectados automaticamente ao TransPerfect GlobalLink Dashboard com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do TransPerfect GlobalLink Dashboard habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O TransPerfect GlobalLink Dashboard dá suporte ao SSO iniciado por **SP e IDP**.
* O TransPerfect GlobalLink Dashboard dá suporte ao provisionamento de usuário **Just-in-Time**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-transperfect-globallink-dashboard-from-the-gallery"></a>Adicionando o TransPerfect GlobalLink Dashboard da galeria

Para configurar a integração do TransPerfect GlobalLink Dashboard ao Azure AD, você precisará adicioná-lo por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **TransPerfect GlobalLink Dashboard** na caixa de pesquisa.
1. Selecione **TransPerfect GlobalLink Dashboard** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-transperfect-globallink-dashboard"></a>Configurar e testar o SSO do Azure AD para o TransPerfect GlobalLink Dashboard

Configure e teste o SSO do Azure AD com o TransPerfect GlobalLink Dashboard usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TransPerfect GlobalLink Dashboard.

Para configurar e testar o SSO do Azure AD com o TransPerfect GlobalLink Dashboard, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do TransPerfect GlobalLink Dashboard](#configure-transperfect-globallink-dashboard-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do TransPerfect GlobalLink Dashboard](#create-transperfect-globallink-dashboard-test-user)** – para ter um equivalente de B.Fernandes no TransPerfect GlobalLink Dashboard que esteja vinculado à representação da usuária no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **TransPerfect GlobalLink Dashboard**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://sso.transperfect.com`

1. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao TransPerfect GlobalLink Dashboard.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TransPerfect GlobalLink Dashboard**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-transperfect-globallink-dashboard-sso"></a>Configurar o SSO do TransPerfect GlobalLink Dashboard

Para configurar o logon único no lado do **TransPerfect GlobalLink Dashboard**, é necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do TransPerfect GlobalLink Dashboard](mailto:TechOps_Consulting@transperfect.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-transperfect-globallink-dashboard-test-user"></a>Criar usuário de teste do TransPerfect GlobalLink Dashboard

Nesta seção, uma usuária chamada B.Fernandes será criada no TransPerfect GlobalLink Dashboard. O TransPerfect GlobalLink Dashboard dá suporte ao provisionamento Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir no TransPerfect GlobalLink Dashboard, o usuário será criado quando você tentar acessar o aplicativo.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do TransPerfect GlobalLink Dashboard, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do TransPerfect GlobalLink Dashboard, na qual você iniciará o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao TransPerfect GlobalLink Dashboard, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do TransPerfect GlobalLink Dashboard em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao TransPerfect GlobalLink Dashboard para o qual o SSO foi configurado. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o TransPerfect GlobalLink Dashboard, você poderá impor controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
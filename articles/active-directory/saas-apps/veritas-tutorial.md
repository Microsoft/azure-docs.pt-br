---
title: 'Tutorial: Integração do Azure Active Directory com o Veritas Enterprise Vault.cloud SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Veritas Enterprise Vault.cloud SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2796928316197bd48723253dbc97dfcd34ac95e8
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222275"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Tutorial: Integração do Azure Active Directory com o Veritas Enterprise Vault.cloud SSO

Neste tutorial, você aprenderá a integrar o Veritas Enterprise Vault.cloud SSO ao Azure AD (Azure Active Directory). Ao integrar o Veritas Enterprise Vault.cloud SSO ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Veritas Enterprise Vault.cloud SSO.
* Permitir que os usuários entrem automaticamente no Veritas Enterprise Vault.cloud SSO com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Veritas Enterprise Vault.cloud SSO, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Veritas Enterprise Vault.cloud SSO habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Veritas Enterprise Vault.cloud SSO dá suporte ao SSO iniciado por **SP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Adicionar o Veritas Enterprise Vault.cloud SSO por meio da galeria

Para configurar a integração do Veritas Enterprise Vault.cloud SSO ao Azure AD, você precisará adicionar o Veritas Enterprise Vault.cloud SSO da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Veritas Enterprise Vault.cloud SSO** na caixa de pesquisa.
1. Selecione **Veritas Enterprise Vault.cloud SSO** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>Configurar e testar o SSO do Azure AD para o Veritas Enterprise Vault.cloud SSO

Configure e teste o SSO do Azure AD com o SSO do Veritas Enterprise Vault.cloud SSO por meio de um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Veritas Enterprise Vault.cloud SSO.

Para configurar e testar o SSO do Azure AD com o Veritas Enterprise Vault.cloud SSO, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Veritas Enterprise Vault.cloud SSO](#configure-veritas-enterprise-vaultcloud-sso-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Veritas Enterprise Vault.cloud SSO](#create-veritas-enterprise-vaultcloud-sso-test-user)** : para ter um equivalente de B.Fernandes no Veritas Enterprise Vault.cloud SSO que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Veritas Enterprise Vault.cloud SSO**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Na caixa **Identificador**, digite uma das URLs de acordo com o Datacenter:

    | Datacenter| URL |
    |----------|----|
    | América do Norte| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Pacífico Asiático| `https://auth.syd.archivecloud.net`|

    c. Na caixa de texto **URL de Resposta**, digite uma das URLs de acordo com o Datacenter:

    | Datacenter| URL |
    |----------|----|
    | América do Norte| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Pacífico Asiático| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Entre em contato com a [equipe de suporte do Cliente do Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Veritas Enterprise Vault.cloud SSO**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Veritas Enterprise Vault.cloud SSO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Veritas Enterprise Vault.cloud SSO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>Configurar o SSO do Veritas Enterprise Vault.cloud SSO

Para configurar o logon único no lado do **Veritas Enterprise Vault.cloud SSO**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Criar um usuário de teste do Veritas Enterprise Vault.cloud SSO

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Veritas Enterprise Vault.cloud SSO. Trabalhe com a [equipe de suporte do Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) para adicionar os usuários na plataforma do Veritas Enterprise Vault.cloud SSO. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Veritas Enterprise Vault.cloud SSO, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Veritas Enterprise Vault.cloud SSO e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Veritas Enterprise Vault.cloud SSO em Meus Aplicativos, você será redirecionado para a URL de Logon do Veritas Enterprise Vault.cloud SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SSO do Veritas Enterprise Vault.cloud SSO, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao GitHub Enterprise Cloud – Conta Empresarial | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GitHub Enterprise Cloud – Conta Empresarial.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: jeedes
ms.openlocfilehash: 7360ad5abc7342043152c2da11038b624b0bfadc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649933"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao GitHub Enterprise Cloud – Conta Empresarial

Neste tutorial, você aprenderá a integrar o GitHub Enterprise Cloud – Conta Empresarial ao Azure AD (Azure Active Directory). Ao integrar o GitHub Enterprise Cloud – Conta Empresarial ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso a uma Conta Empresarial do GitHub e a todas as organizações na Conta Enterprise.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma [Conta do GitHub Enterprise](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts).
* Uma conta de usuário do GitHub que seja um proprietário de Conta Enterprise. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O GitHub Enterprise Cloud – Conta Empresarial dá suporte ao SSO iniciado por **SP** e **IDP**.
* O GitHub Enterprise Cloud – Conta Empresarial dá suporte ao provisionamento de usuário **Just-In-Time**.

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Adicionar o GitHub Enterprise Cloud – Conta Empresarial da galeria

Para configurar a integração do GitHub Enterprise Cloud – Conta Empresarial com o Azure AD, você precisará adicioná-lo à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **GitHub Enterprise Cloud – Conta Empresarial** na caixa de pesquisa.
1. Selecione **GitHub Enterprise Cloud – Conta Empresarial** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Configurar e testar o SSO do Azure AD para o GitHub Enterprise Cloud – Conta Empresarial

Configure e teste o SSO do Azure AD com o GitHub Enterprise Cloud – Conta Empresarial usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no GitHub Enterprise Cloud – Conta Empresarial.

Para configurar e testar o SSO do Azure AD com o GitHub Enterprise Cloud – Conta Empresarial, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário do Azure AD e a conta de usuário de teste ao aplicativo GitHub](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** : para habilitar sua conta de usuário e o usuário de teste `B.Simon` a usar o logon único do Azure AD.
1. **[Habilitar e testar o SAML para a conta Enterprise e as respectivas organizações](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Testar o SSO com outra conta de membro da organização ou proprietário da conta empresarial](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** : para confirmar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **GitHub Enterprise Cloud – Conta Empresarial**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

     Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Substitua `<ENTERPRISE-SLUG>` pelo nome real da sua Conta Empresarial do GitHub.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificateBase64.png)

1. Na seção **Configurar o GitHub Enterprise Cloud – Conta Empresarial**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado `B.Simon`.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Atribuir o usuário do Azure AD e a conta de usuário de teste ao aplicativo GitHub

Nesta seção, você permitirá que `B.Simon` e a conta de usuário usem o logon único do Azure permitindo acesso ao GitHub Enterprise Cloud – Conta Empresarial.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **GitHub Enterprise Cloud – Conta Empresarial**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** e a conta de usuário na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Habilitar e testar o SAML para a Conta Enterprise e as respectivas organizações

Para configurar o logon único no lado do **GitHub Enterprise Cloud – Conta Empresarial**, siga as etapas listadas [nesta documentação do GitHub](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account). 
1. Entre no GitHub.com com uma conta de usuário que seja um [proprietário de conta empresarial](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner). 
1. Copie o valor do campo `Login URL` no aplicativo no portal do Azure e cole-o no campo `Sign on URL` nas configurações de SAML da Conta Empresarial do GitHub. 
1. Copie o valor do campo `Azure AD Identifier` no aplicativo no portal do Azure e cole-o no campo `Issuer` nas configurações de SAML da Conta Empresarial do GitHub. 
1. Copie o conteúdo do arquivo **Certificado (Base64)** baixado nas etapas acima do portal do Azure e cole-o no campo apropriado nas configurações de SAML da Conta Empresarial do GitHub. 
1. Clique em `Test SAML configuration` e confirme se você pode se autenticar por meio da Conta Empresarial do GitHub no Azure AD com êxito.
1. Depois que o teste for bem-sucedido, salve as configurações. 
1. Depois de se autenticar por meio do SAML pela primeira vez na Conta Empresarial do GitHub, uma _identidade externa vinculada_ será criada na Conta Empresarial do GitHub que associa a conta de usuário do GitHub conectada à conta de usuário do Azure AD.  
 
Depois que você habilitar o SSO do SAML para a sua Conta Empresarial do GitHub, o SSO do SAML será habilitado por padrão em todas as organizações pertencentes à sua Conta Corporativa. Todos os membros precisarão se autenticar usando o SSO do SAML para obter acesso às organizações em que são membros, e os proprietários empresariais precisarão se autenticar usando o SSO do SAML ao acessar uma Conta Empresarial.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Testar o SSO com outra conta de membro da organização ou proprietário da conta empresarial

Depois que a integração do SAML estiver configurada para a conta empresarial do GitHub (que também se aplica às organizações do GitHub na conta empresarial), outros proprietários da conta empresarial atribuídos ao aplicativo no Azure AD devem conseguir navegar até a URL da conta empresarial do GitHub (`https://github.com/enterprises/<enterprise account>`), autenticar-se por meio do SAML e acessar as políticas e as configurações na conta empresarial do GitHub. 

Um proprietário de uma organização em uma conta empresarial deve conseguir [convidar um usuário para participar da organização do GitHub](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization). Entre no GitHub.com com uma conta de proprietário da organização e siga as etapas descritas no artigo para convidar `B.Simon` para a organização. Será necessário criar uma conta de usuário do GitHub para `B.Simon` se ainda não houver uma. 

Para testar o acesso à organização do GitHub na Conta Empresarial com a conta de usuário de teste `B.Simon`:
1. Convide `B.Simon` para uma organização na Conta Empresarial como um proprietário da organização. 
1. Entre no GitHub.com usando a conta de usuário que deseja vincular à conta de usuário `B.Simon` do Azure AD.
1. Entre no Azure AD usando a conta de usuário `B.Simon`.
1. Acesse a organização do GitHub. O usuário precisará se autenticar por meio do SAML. Após a autenticação SAML bem-sucedida, `B.Simon` deverá conseguir acessar os recursos da organização. 

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o GitHub Enterprise Cloud – Conta Empresarial, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

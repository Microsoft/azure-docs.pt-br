---
title: 'Tutorial: Integração de SSO do Azure AD com Smart Global Governance'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Smart Global Governance.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: 5a2169db47ca5a6adcddbcc9558161370b896a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997062"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Smart Global Governance

Neste tutorial, você aprenderá a integrar o Smart Global Governance ao Azure AD (Azure Active Directory). Ao integrar o Smart Global Governance ao Azure AD, você pode:

* Use o Azure AD para controlar quem pode acessar o Smart Global Governance.
* Permitir que os usuários sejam conectados automaticamente ao Smart Global Governance com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Smart Global Governance com SSO (logon único) habilitado.

## <a name="tutorial-description"></a>Descrição do tutorial

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

O Smart Global Governance é compatível com SSO iniciado por SP e IDP.

Após configurar o Smart Global Governance, você poderá impor o controle de sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-smart-global-governance-from-the-gallery"></a>Adicionar o Smart Global Governance da galeria

Para configurar a integração do Smart Global Governance ao Azure AD, você precisa adicionar o Smart Global Governance da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta corporativa ou de estudante ou com uma conta Microsoft pessoal.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Acesse **Aplicativos empresariais** e, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, insira **Smart Global Governance** na caixa de pesquisa.
1. Selecione **Smart Global Governance** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>Configurar e testar o SSO do Azure AD para Smart Global Governance

Você vai configurar e testar o SSO do Azure AD com a Smart Global Governance usando um usuário de teste chamado B.Fernandes. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente do Smart Global Governance.

Para configurar e testar o SSO do Azure AD com o Smart Global Governance, você seguirá estas etapas de alto nível:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
    1. **[Permita acesso ao usuário de teste](#grant-access-to-the-test-user)** para permitir que o usuário use o logon único do Azure AD.
1. **[Configurar o SSO do Smart Global Governance](#configure-smart-global-governance-sso)** no lado do aplicativo.
    1. **[Criar um usuário de teste do Smart Global Governance](#create-a-smart-global-governance-test-user)** como um equivalente à declaração do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Smart Global Governance**, na seção **Gerenciar**, selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o botão de lápis da **Configuração Básica de SAML** para editar as configurações:

   ![Botão de lápis para configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por IdP execute as etapas a seguir.

    a. Na caixa **Identificador**, insira uma destas URLs:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. Na caixa **URL de resposta**, insira uma destas URLs:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. Caso deseje configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais** e realize a etapa a seguir.

   - Na caixa **URL de logon**, insira uma destas URLs:

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Download** para **Certificado (Bruto)** para baixar o certificado e salvá-lo no computador:

    ![Link de download do certificado](common/certificateraw.png)

1. Na seção **Configurar Smart Global Governance**, copie as URLs apropriadas conforme seus requisitos:

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory**. Selecione **Usuários** e, em seguida, selecione **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, conclua estas etapas:
   1. Na caixa **Nome**, insira **B.Fernandes**.  
   1. Na caixa **Nome de usuário**, insira \<username>@\<companydomain>.\<extension>. Por exemplo, `B.Simon@contoso.com`.
   1. Marque **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="grant-access-to-the-test-user"></a>Permitir acesso ao usuário de teste

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder a esse usuário o acesso ao Smart Global Governance.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Smart Global Governance**.
1. Na página de visão geral do aplicativo, na seção **Gerenciar**, selecione **Usuários e grupos**:

   ![Selecionar Usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**:

    ![Selecione Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de **Usuários** e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-smart-global-governance-sso"></a>Configurar o SSO do Smart Global Governance

Para configurar o logon único no lado do Smart Global Governance, é necessário enviar o certificado bruto baixado e as URLs apropriadas que você copiou do portal do Azure para a equipe de suporte do [Smart Global Governance](mailto:support.tech@smartglobal.com). Ela configura a conexão de SSO do SAML para que esteja correta em ambos os lados.

### <a name="create-a-smart-global-governance-test-user"></a>Criar um usuário de teste do Smart Global Governance

Trabalhe com a [equipe de suporte do Smart Global Governance](mailto:support.tech@smartglobal.com) para adicionar um usuário chamado B.Fernandes no Smart Global Governance. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao selecionar no bloco Smart Global Governance no Painel de Acesso, você deve entrar automaticamente na instância do Smart Global Governance para o qual você configura o SSO. Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Smart Global Governance com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Smart Global Governance com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
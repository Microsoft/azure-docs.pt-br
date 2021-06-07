---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SiteIntel | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Azure Active Directory e o SiteIntel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c8224bf84a2235086d941df7d02ab6f458f3f16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92509957"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SiteIntel

Neste tutorial, você aprenderá a integrar o SiteIntel ao Azure AD (Azure Active Directory). Ao integrar o SiteIntel ao Azure AD, você pode:

* Controlar no Azure AD quem terá acesso ao SiteIntel.
* Habilitar seus usuários para que entrem automaticamente no SiteIntel com suas contas do Azure AD.
* gerenciar suas contas em um local central, o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do SiteIntel habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SiteIntel é compatível com o SSO iniciado por SP e IdP.
* Depois de configurar o SiteIntel, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>Adicionar o SiteIntel da galeria

Para configurar a integração do SiteIntel ao Azure AD, você precisará adicionar o SiteIntel da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na caixa **Adicionar da galeria**, insira **SiteIntel**.
1. Na lista de resultados, selecione **SiteIntel** e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Configurar e testar o logon único do Azure AD para o SiteIntel

Configure e teste o SSO do Azure AD com o SiteIntel usando um usuário de teste chamado *B.Fernandes*. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SiteIntel.

Para configurar e testar o SSO do Azure AD com o SiteIntel, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.  

    a. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com o usuário B.Fernandes.  

    b. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que o usuário B.Fernandes use o logon único do Azure AD.

1. **[Configurar o SSO do SiteIntel](#configure-siteintel-sso)** – para definir as configurações de logon único no lado do aplicativo.

    * **[Criar um usuário de teste do SiteIntel](#create-a-siteintel-test-user)** para ter um equivalente do usuário B.Fernandes no SiteIntel que esteja vinculado à declaração do usuário do Azure AD.

1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SiteIntel**, vá para a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, ao lado de **Configuração Básica do SAML**, selecione **Editar** (ícone de caneta).

   ![Captura de tela do painel "Configurar Logon Único com SAML"](common/edit-urls.png)

1. Para configurar o aplicativo no modo iniciado por IdP, na seção **Configuração Básica do SAML**, faça o seguinte:

    a. Na caixa **Identificador**, digite uma URL no seguinte formato: `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. Na caixa **URL de Resposta**, digite uma URL no seguinte formato: `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. Na caixa **Estado de Retransmissão**, digite uma URL no seguinte formato: `https://<CLIENT>.siteintel.com`

1. Para configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais** e faça o seguinte:

   * Na caixa **URL de Entrada**, digite um URL usando o seguinte formato: `https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize-os com o Identificador, a URL de Resposta, a URL de Logon e o Estado de retransmissão reais. Para obter esses valores, entre em contato com a [equipe de suporte ao cliente do SiteIntel](mailto:support@intalytics.com). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o botão **Copiar** para copiar a URL na caixa **URL de Metadados de Federação de Aplicativos**.

    ![Captura de tela do botão Copiar da "URL de Metadados de Federação do Aplicativo"](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior do painel.
1. Nas propriedades do **Usuário**, faça o seguinte:

   a. Na caixa **Nome**, insira **B.Fernandes**.  

   b. Na caixa **Nome de usuário**, insira o nome de usuários no seguinte formato: `username@companydomain.extension` (por exemplo, `B.Simon@contoso.com`).

   c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

   d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o usuário B.Fernandes use o logon único do Azure permitindo acesso ao SiteIntel.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de **Aplicativos**, selecione **SiteIntel**.
1. Na página de visão geral do aplicativo, na seção **Gerenciar**, selecione **Usuários e grupos**.

   ![Captura de tela do link "Usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Captura de tela do botão "Adicionar usuário"](common/add-assign-user.png)

1. No painel **Usuários e grupos**, selecione **B.Fernandes** e, em seguida, selecione o botão **Selecionar** na parte inferior da tela.
1. Se você espera um valor de função na declaração SAML, no painel **Selecionar Função**, selecione a função apropriada para o usuário na lista e, em seguida, selecione o botão **Selecionar**.
1. No painel **Adicionar Atribuição**, selecione o botão **Atribuir**.

## <a name="configure-siteintel-sso"></a>Configurar o SSO do SiteIntel

Para configurar o logon único no lado do SiteIntel, envie a URL que você copiou da caixa **URL de Metadados de Federação do Aplicativo** para a equipe de suporte do [SiteIntel](mailto:support@intalytics.com). Ela define esse valor para estabelecer a conexão de SSO do SAML corretamente em ambos os lados.

### <a name="create-a-siteintel-test-user"></a>Criar um usuário de teste do SiteIntel

Nesta seção, você criará um usuário chamado *Brenda Fernandes* no SiteIntel. Trabalhe com a [equipe de suporte do SiteIntel](mailto:support@intalytics.com) para adicionar os usuários na plataforma do SiteIntel. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do **SiteIntel** no Painel de Acesso, você deverá ser conectado automaticamente ao SiteIntel, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](./tutorial-list.md)
- [O que são o acesso a aplicativos e o logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
- [Avaliar o SiteIntel com o Azure AD](https://aad.portal.azure.com/)
- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
- [Como proteger o SiteIntel com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
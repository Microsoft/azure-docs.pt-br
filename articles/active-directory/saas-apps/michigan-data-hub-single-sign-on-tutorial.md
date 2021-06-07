---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Michigan Data Hub Single Sign-On | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Michigan Data Hub Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2020
ms.author: jeedes
ms.openlocfilehash: acdfd8b6c57ad2005f116ffb1e5a3c94a5cb87f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522624"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-michigan-data-hub-single-sign-on"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Michigan Data Hub Single Sign-On

Neste tutorial, você aprenderá a integrar o Michigan Data Hub Single Sign-On ao Azure AD (Azure Active Directory). Ao integrar o Michigan Data Hub Single Sign-On ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Michigan Data Hub Single Sign-On.
* Permitir que os usuários sejam conectados automaticamente ao Michigan Data Hub Single Sign-On usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Michigan Data Hub Single Sign-On.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Michigan Data Hub Single Sign-On dá suporte ao SSO iniciado por **SP**
* Depois de configurar o Michigan Data Hub Single Sign-On, você poderá impor um controle de sessão, que protege contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-michigan-data-hub-single-sign-on-from-the-gallery"></a>Adicionar o Michigan Data Hub Single Sign-On da galeria

Para configurar a integração do Michigan Data Hub Single Sign-On ao Azure AD, você precisará adicionar o Michigan Data Hub Single Sign-On por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Michigan Data Hub Single Sign-On** na caixa de pesquisa.
1. Selecione **Michigan Data Hub Single Sign-On** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-michigan-data-hub-single-sign-on"></a>Configurar e testar o SSO do Azure AD para o Michigan Data Hub Single Sign-On

Configure e teste o SSO do Azure AD com o Michigan Data Hub Single Sign-On usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Michigan Data Hub Single Sign-On.

Para configurar e testar o SSO do Azure AD com o Michigan Data Hub Single Sign-On, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Michigan Data Hub Single Sign-On](#configure-michigan-data-hub-single-sign-on-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Michigan Data Hub Single Sign-On](#create-michigan-data-hub-single-sign-on-test-user)** : para ter um equivalente de B.Fernandes no Michigan Data Hub Single Sign-On que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Michigan Data Hub Single Sign-On**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite a URL: `https://launchpad.midatahub.org`

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Michigan Data Hub Single Sign-On.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Michigan Data Hub Single Sign-On**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-michigan-data-hub-single-sign-on-sso"></a>Configurar o SSO do Michigan Data Hub Single Sign-On

Para configurar o logon único no lado do **Michigan Data Hub Single Sign-On**, você precisará enviar a **URL de Metadados de Federação de Aplicativos** para a [equipe de suporte do Michigan Data Hub Single Sign-On](mailto:support@midatahub.org). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-michigan-data-hub-single-sign-on-test-user"></a>Criar o usuário de teste do Michigan Data Hub Single Sign-On

Nesta seção, você criará um usuário chamado B.Fernandes no Michigan Data Hub Single Sign-On. Trabalhe com a [equipe de suporte do Michigan Data Hub Single Sign-On](mailto:support@midatahub.org) para adicionar os usuários na plataforma do Michigan Data Hub Single Sign-On. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Michigan Data Hub Single Sign-On no Painel de Acesso, você deverá entrar automaticamente no Michigan Data Hub Single Sign-On para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Michigan Data Hub Single Sign-On com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Michigan Data Hub Single Sign-On com visibilidade avançada e controles](/cloud-app-security/proxy-intro-aad)
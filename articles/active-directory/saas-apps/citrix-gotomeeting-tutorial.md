---
title: 'Tutorial: Integração do Azure Active Directory com o GoToMeeting | Microsoft Docs'
description: Saiba quais etapas você precisa executar para integrar o GoToMeeting ao Azure AD (Azure Active Directory).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: ffc87b311cc1eeaa9c0d5bb66602b824b5ee9803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455994"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-gotomeeting"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao GoToMeeting

Neste tutorial, você aprenderá como integrar o GoToMeeting ao Azure AD (Azure Active Directory). Ao integrar o GoToMeeting ao Azure AD, você poderá:

* Controlar quem tem acesso ao GoToMeeting no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao GoToMeeting com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do GoToMeeting.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O GoToMeeting é compatível com SSO iniciado por **IDP**.
* Após configurar o GoToMeeting, você poderá impor controles de sessão, que protegem o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-gotomeeting-from-the-gallery"></a>Adicionar o GoToMeeting da galeria

Para configurar a integração do GoToMeeting ao Azure AD, você precisa adicionar o GoToMeeting, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **GoToMeeting** na caixa de pesquisa.
1. Selecione **GoToMeeting** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-gotomeeting"></a>Configurar e testar o logon único do Azure AD para o GoToMeeting

Configure e teste o SSO do Azure AD com o GoToMeeting usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do GoToMeeting.

Para configurar e testar o SSO do Azure AD com o GoToMeeting, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do GoToMeeting](#configure-gotomeeting-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do GoToMeeting](#create-gotomeeting-test-user)** – para ter um equivalente de B.Fernandes no GoToMeeting que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **GoToMeeting**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://authentication.logmeininc.com/saml/sp`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://authentication.logmeininc.com/saml/acs`

    c. Clique em **Definir URLs adicionais** e configure as URLs abaixo

    d. **URL de Entrada** (mantenha isso em branco)

    e. Na caixa de texto **RelayState**, digite uma URL usando o seguinte padrão:

   - Para GoToMeeting App, utilize `https://global.gotomeeting.com`

   - Para GoToTraining, utilize `https://global.gototraining.com`

   - Para GoToWebinar, utilize `https://global.gotowebinar.com` 

   - Para GoToAssist, utilize `https://app.gotoassist.com`

     > [!NOTE]
     > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do GoToMeeting](../manage-apps/view-applications-portal.md) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o GoToMeeting**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff


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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao GoToMeeting.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **GoToMeeting**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-gotomeeting-sso"></a>Configurar o SSO do GoToMeeting

1. Em uma janela de navegador diferente, faça logon no [GoToMeeting Organization Center](https://organization.logmeininc.com/). Você será solicitado a confirmar que o IdP foi atualizado.

2. Habilite a caixa de seleção "Meu provedor de identidade foi atualizado com o novo domínio". Clique em **Concluído** quando terminar.

### <a name="create-gotomeeting-test-user"></a>Criar um usuário de teste do GoToMeeting

Nesta seção, é criado um usuário chamado Brenda Fernandes no GoToMeeting. O GoToMeeting dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Se um usuário ainda não existir no GoToMeeting, um novo será criado quando você tentar acessar o GoToMeeting.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [Equipe de suporte do GoToMeeting](https://support.logmeininc.com/gotomeeting).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do GoToMeeting no Painel de Acesso, você deverá ser conectado automaticamente ao GoToMeeting no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o GoToMeeting com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o GoToMeeting com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Harness | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Harness.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.openlocfilehash: d6a6c8b49582b34c2603e0ddf78b76736f97c183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92445572"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Harness

Neste tutorial, você aprenderá a integrar o Harness ao Azure AD (Azure Active Directory). Quando integrar o Harness ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Harness.
* Permitir que os usuários sejam conectados automaticamente ao Harness com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Harness.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Harness dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-harness-from-the-gallery"></a>Adicionar o Harness da galeria

Para configurar a integração do Harness ao Azure AD, você precisará adicionar o Harness da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Harness** na caixa de pesquisa.
1. Selecione **Harness** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Configurar e testar o logon único do Azure AD para o Harness

Configure e teste o SSO do Azure AD com o Harness usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Harness.

Para configurar e testar o SSO do Azure AD com o Harness, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Harness](#configure-harness-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Harness](#create-harness-test-user)** para ter um equivalente de B.Fernandes no Harness que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Harness**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.harness.io/`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Você obterá a URL de Resposta real da seção **Configurar SSO do Harness**, que é explicado posteriormente no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Harness**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Harness.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Harness**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-harness-sso"></a>Configurar SSO do Harness

1. Para automatizar a configuração no Harness, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Instalar o Harness** direcionará você ao aplicativo Harness. Lá, forneça as credenciais de administrador para entrar no Harness. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Harness manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Harness como administrador e execute as seguintes etapas:

4. Na parte superior direita da página, clique em **Segurança Contínua** > **Gerenciamento de Acesso** > **Configurações de Autenticação**.

    ![Captura de tela que mostra o menu "Segurança Contínua" com as opções "Gerenciamento de Acesso" e "Configurações de Autenticação" selecionadas.](./media/harness-tutorial/configure01.png)

5. Na seção **Provedores de SSO**, clique em **+ Adicionar Provedores de SSO** > **SAML**.

    ![Captura de tela que mostra a seção "Provedores de SSO" com a opção "+ Adicionar Provedores de SSO – SAML" selecionada.](./media/harness-tutorial/configure03.png)

6. No pop-up **Provedor do SAML**, siga estas etapas:

    ![Captura de tela que mostra o pop-up "Provedor do SAML" com os campos "URL" e "Nome de exibição" realçados e os botões "Escolher Arquivo" e "Enviar" selecionados.](./media/harness-tutorial/configure02.png)

    a. Copie a instância **Em seu Provedor de SSO, habilite o logon baseado em SAML e insira a seguinte URL** e cole-a na caixa de texto URL de Resposta na seção **Configuração Básica de SAML** no portal do Azure.

    b. Na caixa de texto **Nome de Exibição**, digite o nome de exibição.

    c. Clique em **Escolher arquivo** para fazer upload do arquivo XML de Metadados de Federação, que você baixou do Azure AD.

    d. Clique em **ENVIAR**.

### <a name="create-harness-test-user"></a>Criar usuário de teste do Harness

Para permitir que os usuários do Azure AD entrem no Harness, eles devem ser provisionados no Harness. No Harness, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Harness como administrador.

1. Na parte superior direita da página, clique em **Segurança Contínua** > **Gerenciamento de Acesso** > **Usuários**.

    ![Captura de tela que mostra o menu "Segurança Contínua" com as opções "Gerenciamento de Acesso" e "Usuários" selecionadas.](./media/harness-tutorial/configure04.png)

1. No lado direito da página, clique em **+ Adicionar Usuário**.

    ![Captura de tela que mostra a página "Usuários" com a ação "+ Adicionar Usuário" selecionada.](./media/harness-tutorial/configure05.png)

1. No pop-up **Adicionar usuário**, execute as seguintes etapas:

    ![Configuração do agente](./media/harness-tutorial/configure06.png)

    a. Na caixa de texto **Endereços de Email**, insira o email do usuário como `B.simon@contoso.com`.

    b. Selecione seus **Grupos de usuários**.

    c. Clique em **Enviar**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Harness no Painel de Acesso, você deverá ser conectado automaticamente ao Harness no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Harness com o Azure AD](https://aad.portal.azure.com/)
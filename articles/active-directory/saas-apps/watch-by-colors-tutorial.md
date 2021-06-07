---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Watch by Colors | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Watch by Colors.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.openlocfilehash: fae276049ec3ff6c4bf96be38ff0d2a952731a15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92636775"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Watch by Colors

Neste tutorial, você aprenderá a integrar o Watch by Colors ao Azure AD (Azure Active Directory). Quando integrar o Watch by Colors ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Watch by Colors.
* Permitir que os usuários sejam conectados automaticamente ao Watch by Colors com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Watch by Colors.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Watch by Colors dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-watch-by-colors-from-the-gallery"></a>Adicionar o Watch by Colors da galeria

Para configurar a integração do Watch by Colors ao Azure AD, você precisará adicionar o Watch by Colors da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Watch by Colors** na caixa de pesquisa.
1. Selecione **Watch by Colors** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Configurar e testar o logon único do Azure AD para o Watch by Colors

Configure e teste o SSO do Azure AD com o Watch by Colors usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Watch by Colors.

Para configurar e testar o SSO do Azure AD com o Watch by Colors, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Watch by Colors](#configure-watch-by-colors-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Watch by Colors](#create-watch-by-colors-test-user)** para ter um equivalente de B.Fernandes no Watch by Colors que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Watch by Colors**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado no modo iniciado do **IDP** e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.colorscorporation.com/login`

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Watch by Colors.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Watch by Colors**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-watch-by-colors-sso"></a>Configurar SSO do Watch by Colors

1. Para automatizar a configuração no Watch by Colors, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Configurar o Watch by Colors** direcionará você ao aplicativo Watch by Colors. Nele, forneça as credenciais de administrador para entrar no Watch by Colors. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

3. Se quiser configurar o Watch by Colors manualmente, abra uma nova janela do navegador da Web, entre no site da empresa do Watch by Colors como administrador e execute as seguintes etapas:

4. No canto superior direito da página, clique em **perfil** > **Configurações da Conta** > **SSO (Logon Único)** .

    ![A captura de tela mostra a página Configurações da Conta na qual o SSO está desabilitado.](./media/watch-by-colors-tutorial/config01.png)

5. Na página **SSO (Logon Único)** , siga estas etapas:

    ![A captura de tela mostra a guia Configuração do SAML em que você pode habilitar o SAML.](./media/watch-by-colors-tutorial/config02.png)

    a. Alternar **Habilitar SAML** para **ATIVADO**.

    b. Na caixa de texto **URL**, cole a **URL de metadados da federação** que você copiou do portal do Azure.

    c. Clique em **Importar** e os campos a seguir serão preenchidos automaticamente na página.

    d. Clique em **Save** (Salvar).

### <a name="create-watch-by-colors-test-user"></a>Configurar usuário de teste do Watch by Colors

Para permitir que os usuários do Azure AD entrem no Watch by Colors, eles devem ser provisionados no Watch by Colors. No Watch by Colors, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Watch by Colors como Administrador de Segurança.

1. No canto superior direito da página, clique em **perfil** > **Usuários** > **Adicionar Usuário**.

    ![A captura de tela mostra a página Usuários.](./media/watch-by-colors-tutorial/config03.png)

1. Na página **Detalhes do Usuário**, siga estas etapas:

    ![A captura de tela mostra a página Detalhes do Usuário em que você pode inserir os valores descritos.](./media/watch-by-colors-tutorial/config04.png)

    a. Na caixa de texto **Nome**, insira o nome do usuário como **B**.

    b. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

    c. Na caixa de texto **Email**, insira o email do usuário como `B.Simon@contoso.com`.

    d. Na caixa de texto **Senha**, insira a senha.

    e. Selecione **Permissões de Conta** de acordo com sua organização.

    f. Clique em **Save** (Salvar).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Watch by Colors no Painel de Acesso, você deverá ser conectado automaticamente ao Watch by Colors no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Watch by Colors com o Azure AD](https://aad.portal.azure.com/)
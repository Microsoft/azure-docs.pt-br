---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Evernote | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Evernote.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: 86a314cd5255c06a70d0f9b28d06e3ac4156fdb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453818"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Evernote

Neste tutorial, você aprenderá a integrar o Evernote ao Azure AD (Azure Active Directory). Ao integrar o Evernote ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Evernote.
* Permitir que os usuários sejam conectados automaticamente ao Evernote com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Evernote habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Evernote dá suporte ao SSO iniciado por **SP e IdP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-evernote-from-the-gallery"></a>Adicionar o Evernote da galeria

Para configurar a integração do Evernote ao Azure AD, você precisará adicionar o Evernote da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Evernote** na caixa de pesquisa.
1. Selecione **Evernote** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Configurar e testar o logon único do Azure AD para o Evernote

Configure e teste o SSO do Azure AD com o Evernote usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Evernote.

Para configurar e testar o SSO do Azure AD com o Evernote, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Evernote](#configure-evernote-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Evernote](#create-evernote-test-user)** – para ter um equivalente de B.Fernandes no Evernote que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Evernote**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **Identificador**, digite uma URL: `https://www.evernote.com/saml2`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://www.evernote.com/Login.action`

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Para modificar as opções de **Autenticação**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Captura de tela que mostra a caixa de diálogo "Certificado de Autenticação SAML" com o botão "Editar" selecionado.](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Selecione a opção **Assinar resposta SAML e declaração** para **Opção de Assinatura**.

    b. Clique em **Salvar**

1. Na seção **Configurar o Evernote**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Evernote.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Evernote**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-evernote-sso"></a>Configurar o SSO do Evernote

1. Para automatizar a configuração no Evernote, é necessário instalar a **Extensão do navegador Entrada Segura de Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, um clique em **Configurar o Evernote**. direcionará você ao aplicativo Evernote. Nele, forneça as credenciais de administrador para entrar no Evernote. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Caso deseje configurar o Evernote manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Evernote como administrador e execute as seguintes etapas:

4. Acesse **'Console de Administração'**

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. No **'Console de Administração'** , acesse **'Segurança'** e selecione **'Logon Único'**

    ![SSO-Setting](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Configure os seguintes valores:

    ![Certificate-Setting](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Habilitar SSO:** o SSO está habilitado por padrão (clique em **Desabilitar Logon Único** para remover o requisito de SSO)

    b. Cole o valor da **URL de Logon** copiado do portal do Azure na caixa de texto **URL de Solicitação HTTP do SAML**.

    c. Abra o certificado baixado no Azure AD em um bloco de notas e copie o conteúdo, incluindo "BEGIN CERTIFICATE" e "END CERTIFICATE" e cole-o na caixa de texto **Certificado X.509**. 

    d.Clique em **Salvar Alterações**

### <a name="create-evernote-test-user"></a>Criar um usuário de teste do Evernote

Para permitir que os usuários do Azure AD entrem no Evernote, eles precisarão ser provisionados no Evernote.  
No caso do Evernote, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do Evernote como administrador.

2. Clique no **'Console de Administração'** .

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. No **'Console de Administração'** , acesse **'Adicionar usuários'** .

    ![Captura de tela que mostra o menu "Usuários" com "Adicionar Usuários" selecionado.](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Adicionar membros da equipe** na caixa de texto **Email**, digite o endereço de email da conta de usuário e clique em **Convidar.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Após o envio do convite, o titular da conta do Azure Active Directory receberá um email para aceitar o convite.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Evernote no Painel de Acesso, você deverá ser conectado automaticamente ao Evernote, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Evernote com o Azure AD](https://aad.portal.azure.com/)
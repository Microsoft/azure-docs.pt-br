---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Teamphoria | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Teamphoria.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: a8cf58f49cba1bcc77c96a0da2061a037bb62bf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92504730"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Teamphoria

Neste tutorial, você aprenderá a integrar o Teamphoria ao Azure AD (Azure Active Directory). Ao integrar o Teamphoria ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Teamphoria.
* Permitir que os usuários sejam conectados automaticamente ao Teamphoria com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Teamphoria.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Teamphoria é compatível com o SSO iniciado por **SP**

## <a name="adding-teamphoria-from-the-gallery"></a>Adicionar o Teamphoria da galeria

Para configurar a integração do Teamphoria com o Azure AD, você precisará adicionar o Teamphoria da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Teamphoria** na caixa de pesquisa.
1. Selecione **Teamphoria** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Configurar e testar o logon único do Azure AD para o Teamphoria

Configure e teste o SSO do Azure AD com o Teamphoria usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Teamphoria.

Para configurar e testar o SSO do Azure AD com o Teamphoria, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Teamphoria](#configure-teamphoria-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Teamphoria](#create-teamphoria-test-user)** – para ter um equivalente de B.Fernandes no Teamphoria que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Teamphoria**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do Teamphoria](https://www.teamphoria.com/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Teamphoria**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Teamphoria.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Teamphoria**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-teamphoria-sso"></a>Configurar o SSO do Teamphoria

1. Para automatizar a configuração no Teamphoria, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, um clique em **Configurar o Teamphoria** direcionará você ao aplicativo Teamphoria. Nele, forneça as credenciais de administrador para entrar no Teamphoria. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Caso deseje configurar o Teamphoria manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Teamphoria como administrador e execute as seguintes etapas:

4. Vá até a opção **CONFIGURAÇÕES DE ADMINISTRAÇÃO** na barra de ferramentas à esquerda e, na guia Configurar, clique em **LOGON ÚNICO** para abrir a janela de configuração de SSO.

    ![A captura de tela mostra as CONFIGURAÇÕES DO ADMINISTRADOR, em que você pode selecionar o LOGON ÚNICO.](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Clique na opção **ADICIONAR NOVO PROVEDOR DE IDENTIDADE** no canto superior direito para abrir o formulário para adicionar as configurações de SSO.

    ![A captura de tela mostra onde você pode selecionar a opção ADICIONAR NOVO PROVEDOR DE IDENTIDADE.](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Insira os detalhes nos campos conforme descrito abaixo:

    ![A captura de tela mostra a página em que você pode inserir os valores descritos.](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOME DE EXIBIÇÃO**: insira o nome de exibição do plug-in na página de administração.

    b. **NOME DO BOTÃO**: o nome da guia que será exibida na página de logon para entrar usando SSO.

    c. **CERTIFICADO**: abra o certificado baixado anteriormente do portal do Azure no bloco de notas, copie o conteúdo e cole-o nesta caixa.

    d. **PONTO DE ENTRADA**: Cole a **URL de logon** copiada anteriormente do portal do Azure.

    e. Alterne a opção para **LIGADO** e clique em **SALVAR**.

### <a name="create-teamphoria-test-user"></a>Criar usuário de teste do Teamphoria

Para permitir que usuários do Azure AD entrem no Teamphoria, eles deverão ser provisionados no Teamphoria. No caso do Teamphoria, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do Teamphoria como administrador.

1. Clique nas configurações de **ADMIN** na barra de ferramentas à esquerda e, na guia **GERENCIAR**, clique em **USUÁRIOS** para abrir a página de administração para usuários.

    ![Adicionar Funcionário](./media/teamphoria-tutorial/admin_manage_users.png)

1. Clique na opção **CONVITE MANUAL**.

    ![A captura de tela mostra a opção CONVITE MANUAL.](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Nessa página, realize a ação a seguir.

    ![A captura de tela mostra a página CONVITE MANUAL DO USUÁRIO, em que você pode inserir o nome e o endereço de email.](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Na caixa de texto **ENDEREÇO DE EMAIL**, insira o **endereço de email** do usuário como B.Fernandes.

    b. Na caixa de texto **NOME**, insira o nome do usuário como **B**.

    c. Na caixa de texto **SOBRENOME**, digite o sobrenome do usuário como **Fernandes**.

    d. Clique em **CONVIDAR 1 USUÁRIO**. O usuário deve aceitar o convite para ser criado no sistema.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Teamphoria no Painel de Acesso, você deverá entrar automaticamente no Teamphoria para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Teamphoria com o Azure AD](https://aad.portal.azure.com/)
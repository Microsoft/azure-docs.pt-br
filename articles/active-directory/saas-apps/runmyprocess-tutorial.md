---
title: 'Tutorial: Integração do Azure Active Directory com o RunMyProcess | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RunMyProcess.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 011bbbda07806f1493ae27fbeef8509f0d12c44b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518442"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Tutorial: Integrar o RunMyProcess ao Azure Active Directory

Neste tutorial, você aprende a integrar o RunMyProcess ao Azure AD (Azure Active Directory). Ao integrar o RunMyProcess ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao RunMyProcess.
* Permitir que os usuários sejam conectados automaticamente ao RunMyProcess com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do RunMyProcess.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O RunMyProcess é compatível com o SSO iniciado por **SP**

## <a name="adding-runmyprocess-from-the-gallery"></a>Adicionando o RunMyProcess da galeria

Para configurar a integração do RunMyProcess ao Azure AD, você precisa adicionar o RunMyProcess por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **RunMyProcess** na caixa de pesquisa.
1. Selecione **RunMyProcess** no painel de resultados e, depois, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o RunMyProcess usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do RunMyProcess.

Para configurar e testar o SSO do Azure AD com o RunMyProcess, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do RunMyProcess](#configure-runmyprocess-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do RunMyProcess](#create-runmyprocess-test-user)** – para ter um equivalente de B.Fernandes no RunMyProcess que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **RunMyProcess**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do RunMyProcess](mailto:support@runmyprocess.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o RunMyProcess**, copie as URLs apropriadas com base em suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Configurar o SSO do RunMyProcess

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do RunMyProcess como um administrador.

1. No painel de navegação esquerdo, clique em **Conta** e selecione **Configuração**.

    ![Captura de tela que mostra a opção Configuração selecionada em Conta.](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Vá para a seção **Método de autenticação** e execute as etapas a seguir:

    ![Captura de tela que mostra a guia Método de autenticação, na qual você pode inserir os valores descritos.](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Em **Método**, selecione **SSO com Samlv2**.

    b. Na caixa de texto **Redirecionamento de SSO**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **Redirecionamento de logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Na caixa de texto **Formato da ID de Nome**, digite o valor do **Formato do Identificador de Nome** como **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    e. Abra o arquivo de certificado baixado do portal do Azure no Bloco de notas, copie o conteúdo do arquivo de certificado e cole-o na caixa de texto **Certificado**.

    f. Clique no ícone **Salvar**.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao RunMyProcess.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **RunMyProcess**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-runmyprocess-test-user"></a>Criar usuário de teste no RunMyProcess

Para permitir que os usuários do Azure AD entrem no RunMyProcess, eles devem ser provisionados no RunMyProcess. No caso do RunMyProcess, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do RunMyProcess como administrador.

1. Clique em **Conta** e selecione **Usuários** no painel de navegação esquerdo, clique em **Novo Usuário**.

    ![Novo usuário](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Novo Usuário")

1. Na seção **Configurações do Usuário** , realize as seguintes etapas:

    ![Perfil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Perfil")
  
    a. Digite o **Nome** e o **Email** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas.

    b. Selecione uma **Linguagem IDE**, um **Idioma** e um **Perfil**.

    c. Selecione **Enviar email de criação da conta para mim**.

    d. Clique em **Save** (Salvar).

    > [!NOTE]
    > Você pode usar qualquer outra ferramenta de criação da conta de usuário do RunMyProcess ou as APIs fornecidas pelo RunMyProcess para provisionar as contas de usuário do Active Directory do Azure.

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do RunMyProcess no Painel de Acesso, você deverá ser conectado automaticamente ao RunMyProcess, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
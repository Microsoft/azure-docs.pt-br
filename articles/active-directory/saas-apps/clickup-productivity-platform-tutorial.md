---
title: 'Tutorial: Integração do Azure Active Directory com a ClickUp Productivity Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Software de Produtividade de ClickUp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 29bc02466825aa2ea2c1a9ece2826b1262293392
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285120"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutorial: Integração do Azure Active Directory com a ClickUp Productivity Platform

Neste tutorial, você aprenderá a integrar o ClickUp Productivity Platform ao Azure AD (Azure Active Directory). Ao integrar o ClickUp Productivity Platform ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao ClickUp Productivity Platform.
* Permitir que os usuários entrem automaticamente no ClickUp Productivity Platform com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do ClickUp Productivity Platform habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ClickUp Productivity Platform dá suporte ao SSO iniciado por **SP**.

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>Adicionar o ClickUp Productivity Platform por meio da galeria

Para configurar a integração da ClickUp Productivity Platform ao Azure AD, você precisará adicionar a ClickUp Productivity Platform da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ClickUp Productivity Platform** na caixa de pesquisa.
1. Selecione **ClickUp Productivity Platform** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Configurar e testar o SSO do Azure AD para o ClickUp Productivity Platform

Configure e teste o SSO do Azure AD com o ClickUp Productivity Platform por meio de um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ClickUp Productivity Platform.

Para configurar e testar o SSO do Azure AD com o ClickUp Productivity Platform, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ClickUp Productivity Platform](#configure-clickup-productivity-platform-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do ClickUp Productivity Platform](#create-clickup-productivity-platform-test-user)** : para ter um equivalente de B.Fernandes no ClickUp Productivity Platform que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **ClickUp Productivity Platform**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://app.clickup.com/login/sso`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize esse valor com o Identificador real, que é explicado posteriormente neste tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao ClickUp Productivity Platform.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ClickUp Productivity Platform**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-clickup-productivity-platform-sso"></a>Configurar o SSO do ClickUp Productivity Platform

1. Em uma janela de navegador da Web diferente, entre com seu locatário da Plataforma de Produtividade ClickUp como administrador.

2. Clique em **Perfil do usuário** e selecione **Configurações**.

    ![Captura de tela que mostra o locatário de Produtividade ClickUp com o ícone Configurações selecionado.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Captura de tela que mostra Configurações.](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. Selecione **Microsoft** em um único Provedor de SSO (Logon Único).

    ![Captura de tela que mostra o painel Autenticação com Microsoft selecionado.](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. Na página **Configurar Logon Único da Microsoft**, execute as seguintes etapas:

    ![Captura de tela que mostra a página Configurar Logon Único da Microsoft, na qual você pode copiar a ID da Entidade e salvar o URL de Metadados de Federação do Azure.](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Clique em **Copiar** para copiar o valor de ID da entidade e cole-o na caixa de texto **Identificador (ID da Entidade)** na seção **Configuração Básica de SAML** no portal do Azure.

    b. Na caixa de texto **URL de Metadados de Federação do Azure**, cole o valor de URL de Metadados de Federação de Aplicativos que você copiou do portal do Azure e clique em **Salvar**.

5. Para concluir a instalação, clique em **Autenticar com a Microsoft para concluir a instalação** e autenticar com uma conta Microsoft.

    ![Captura de tela que mostra o botão Autenticar com a Microsoft para concluir a instalação.](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>Criar usuário de teste da ClickUp Productivity Platform

1. Em uma janela de navegador da Web diferente, entre com seu locatário da Plataforma de Produtividade ClickUp como administrador.

2. Clique em **Perfil do usuário** e selecione **Pessoas**.

    ![Captura de tela que mostra o locatário de Produtividade do ClickUp.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Captura de tela que mostra o link Pessoas selecionado.](./media/clickup-productivity-platform-tutorial/user-1.png)

3. Insira o endereço de email do usuário na caixa de texto e clique em **Convidar**.

    ![Captura de tela que mostra Configurações dos Usuários da Equipe em que você pode convidar pessoas por email.](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > O usuário receberá a notificação e precisará aceitar o convite para ativar a conta.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do ClickUp Productivity Platform, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do ClickUp Productivity Platform e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do ClickUp Productivity Platform em Meus Aplicativos, você será redirecionado à URL de Logon do ClickUp Productivity Platform. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o ClickUp Productivity Platform, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
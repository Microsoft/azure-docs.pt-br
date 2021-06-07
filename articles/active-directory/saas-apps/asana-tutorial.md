---
title: 'Tutorial: Integração do Azure Active Directory ao Asana | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a06c94eed6c90d7b38f28d37f3c8145d4ac1151d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651001"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integração do Azure Active Directory ao Asana

Neste tutorial, você aprenderá a integrar o Asana ao Azure AD (Azure Active Directory). Ao integrar o Asana ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Asana.
* Permitir que os usuários sejam conectados automaticamente ao Asana com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Asana habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Asana dá suporte ao SSO iniciado por **SP**

* O Asana dá suporte ao [provisionamento **automatizado** de usuários](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Adicionar o Asana por meio da galeria

Para configurar a integração do Asana ao Azure AD, você precisará adicionar o Asana da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Asana** na caixa de pesquisa.
1. Selecione **Asana** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Configurar e testar o SSO do Azure AD para o Asana

Configure e teste o SSO do Azure AD com o Asana usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Asana.

Para configurar e testar o SSO do Azure AD com o Asana, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Asana](#configure-asana-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Asana](#create-asana-test-user)** – para ter um equivalente de B.Fernandes no Asana que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Asana**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Asana](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://app.asana.com/`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://app.asana.com/`

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Asana**, copie a URL apropriada, de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Asana.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Asana**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-asana-sso"></a>Configurar o SSO do Asana

1. Em uma janela de navegador diferente, entre em seu aplicativo Asana. Para configurar o SSO no Asana, acesse as configurações de workspace clicando no nome do workspace no canto superior direito da tela. Em seguida, clique em **\<your workspace name\>Configurações**.

    ![configurações de sso do Asana](./media/asana-tutorial/settings.png)

2. Na janela **Configurações da organização**, clique em **Administração**. Em seguida, clique em **Os membros devem fazer logon por meio de SAML** para habilitar a configuração de SSO. Execute as seguintes etapas:

    ![Definições de Configurar Organização de Logon Único](./media/asana-tutorial/save.png)  

    a. Na caixa de texto **URL da página de entrada**, cole a **URL de logon**.

    b. Clique com o botão direito do mouse no certificado baixado no portal do Azure e, em seguida, abra o arquivo de certificado usando o Bloco de notas ou seu editor de texto preferido. Copie o conteúdo entre o início e o final do título do certificado e cole-o na caixa de texto **Certificado X.509**.

3. Clique em **Save** (Salvar). Vá para [Guia do Asana para configurar SSO](https://asana.com/guide/help/premium/authentication#gl-saml) se precisar de assistência adicional.

### <a name="create-asana-test-user"></a>Criar um usuário de teste do Asana

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Asana. O Asana dá suporte ao provisionamento automático de usuário, que é habilitado por padrão. Você pode encontrar mais detalhes [ aqui ](asana-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

**Se você precisar criar um usuário manualmente, siga estas etapas:**

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Asana.

1. No **Asana**, vá para a seção **Equipes** no painel esquerdo. Clique no botão de sinal de adição.

    ![Criação de um usuário de teste do AD do Azure](./media/asana-tutorial/teams.png)

2. Digite o email do usuário como **brenda.fernandes\@contoso.com** na caixa de texto e, em seguida, selecione **Convidar**.

3. Clique em **Enviar Convite**. O novo usuário receberá um email em sua conta de email. Ele precisará criar e validar a conta.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Asana, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Asana e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Asana em Meus Aplicativos, isso redirecionará você à URL de Logon do Asana. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Asana, você poderá impor um controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
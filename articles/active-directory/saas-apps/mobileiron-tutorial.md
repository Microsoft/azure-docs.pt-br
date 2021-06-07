---
title: 'Tutorial: Integração do Azure Active Directory ao MobileIron | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: c47092b1488a79805db69308bcb9a8efde1c0d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652991"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Tutorial: integração do Azure Active Directory com o MobileIron

 Neste tutorial, você aprenderá a integrar o MobileIron ao Azure AD (Azure Active Directory). Ao integrar o MobileIron ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao MobileIron.
* Permitir que os usuários sejam conectados automaticamente ao MobileIron com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do MobileIron habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O MobileIron dá suporte ao SSO iniciado por **SP e IDP**.

## <a name="add-mobileiron-from-the-gallery"></a>Como adicionar o MobileIron por meio da galeria

Para configurar a integração do MobileIron ao Azure AD, você precisará adicionar o MobileIron da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **MobileIron** na caixa de pesquisa.
1. Selecione **MobileIron** nos resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Configurar e testar o SSO do Azure AD para o MobileIron

Configure e teste o SSO do Azure AD com o MobileIron usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no MobileIron.

Para configurar e testar o SSO do Azure AD com o MobileIron, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
     1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do MobileIron](#configure-mobileiron-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do MobileIron](#create-mobileiron-test-user)** – para ter um equivalente de Brenda Fernandes no MobileIron que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o SSO do Azure AD no portal do Azure.
 
1. No portal do Azure, na página de integração de aplicativos do **MobileIron**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um Método de Logon Único**, selecione **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.MobileIron.com/<key>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

     Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Você obterá os valores de chave e host do portal administrativo do MobileIron, o que é explicada mais adiante no tutorial.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de seleção **Mostrar senha** e, em seguida, anote a senha.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao MobileIron.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **MobileIron**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-mobileiron-sso"></a>Configurar o SSO do MobileIron

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do MobileIron como administrador.

2. Acesse **Administrador** > **Identidade** e selecione a opção **AAD** no campo **Informações sobre a Configuração do IDP de Nuvem**.

    ![A captura de tela mostra a guia Administrador do site do MobileIron com a opção Identidade selecionada.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Copie os valores de **Chave** e **Host** e cole-os para completar as URLs na seção **Configuração Básica do SAML** no portal do Azure.

    ![A captura de tela mostra a opção Configurar o SAML com um valor de chave e host.](./media/MobileIron-tutorial/key.png)

4. Em **Exportar arquivo de metadados do AAD e importar para o Campo de Nuvem MobileIron**, clique **Escolher Arquivo** para carregar os metadados baixados do portal do Azure. Clique em **Concluído** depois de carregar.

    ![Botão Configurar metadados do administrador de Logon Único](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>Criar um usuário de teste do MobileIron

Para permitir que os usuários do Azure AD façam logon no MobileIron, eles devem ser provisionados no MobileIron.  
No caso do MobileIron, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do MobileIron como administrador.

1. Vá para **Usuários** e clique em **Adicionar** > **Usuário Único**.

    ![Botão Configurar usuário do Logon Único](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. Na página do diálogo **“Usuário Único”** , execute as seguintes etapas:

    ![Botão Configurar adicionar usuário de Logon Único](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. Na caixa de texto **Endereço de Email**, insira o email do usuário como brittasimon@contoso.com.

    b. Na caixa de texto **Nome**, insira o nome do usuário como Brenda.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como Fernandes.

    d. Clique em **Concluído**.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do MobileIron, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do MobileIron e inicie o fluxo de logon nela.

### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao MobileIron, para o qual configurou o SSO.

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do MobileIron em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao MobileIron, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o MobileIron, você poderá impor controles de sessão, que fornecem proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

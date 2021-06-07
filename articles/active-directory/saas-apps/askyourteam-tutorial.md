---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AskYourTeam | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: b239811e9fe49f420b5e9d15afafdcf26832dbf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735369"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AskYourTeam

Neste tutorial, você aprenderá a integrar o AskYourTeam ao Azure AD (Azure Active Directory). Ao integrar o AskYourTeam ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao AskYourTeam.
* Permitir que os usuários sejam conectados automaticamente ao AskYourTeam com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do AskYourTeam habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O AskYourTeam dá suporte ao SSO iniciado por **SP e IdP**.

## <a name="adding-askyourteam-from-the-gallery"></a>Como adicionar o AskYourTeam por meio da galeria

Para configurar a integração do AskYourTeam ao Azure AD, você precisará adicionar o AskYourTeam por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AskYourTeam** na caixa de pesquisa.
1. Selecione **AskYourTeam** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>Configurar e testar o SSO do Azure AD para o AskYourTeam

Configure e teste o SSO do Azure AD com o AskYourTeam usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no AskYourTeam.

Para configurar e testar o SSO do Azure AD com o AskYourTeam, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do AskYourTeam](#configure-askyourteam-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do AskYourTeam](#create-askyourteam-test-user)** : para ter um equivalente de B.Fernandes no AskYourTeam que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **AskYourTeam**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com os valores reais da URL de Resposta e da URL de Logon, que serão explicados mais adiante no tutorial.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o AskYourTeam**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao AskYourTeam.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AskYourTeam**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-askyourteam-sso"></a>Configurar o SSO do AskYourTeam

1. Para automatizar a configuração no AskYourTeam, é necessário instalar a **Extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, um clique em **Configurar o AskYourTeam** direcionará você ao aplicativo AskYourTeam. Nele, forneça as credenciais de administrador para entrar no AskYourTeam. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o AskYourTeam manualmente, em outra janela do navegador da Web, entre em seu site da empresa do AskYourTeam como administrador.

1. Clique em **Minha Organização**.

    ![Captura de tela que mostra o link Minha Organização.](./media/askyourteam-tutorial/user1.png)

1. Clique em **Integrações**.

    ![Captura de tela que mostra o link Integrações.](./media/askyourteam-tutorial/configure1.png)

1. Clique em **Editar Configurações**.

    ![Captura de tela que mostra a mensagem Logon Único com um botão Editar Configurações.](./media/askyourteam-tutorial/configure2.png)

1. Na página **Editar a Integração de Logon Único**, execute as seguintes etapas: 

    ![Captura de tela que mostra Editar Integração de Logon Único, em que pode inserir os valores para esta etapa.](./media/askyourteam-tutorial/configure3.png)

    a. Na caixa de texto **URL de Serviço de Logon Único do SAML**, cole o valor da **URL do Logon** copiado do portal do Azure.

    b. Na caixa de texto **ID da Entidade do SAML**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado de Autenticação SAML – Base64**.

    > [!NOTE]
    > Como alternativa, você também pode carregar o arquivo **XML de Metadados de Federação** clicando na opção **Escolher Arquivo**.

    e. Copie o valor da **URL de Resposta (URL do Serviço do Consumidor de Declaração)** e cole esse valor na caixa de texto **URL de Resposta** na seção **Configuração Básica do SAML** no portal do Azure.

    f. Copie a **URL de Logon**, cole esse valor na caixa de texto **URL de Logon** na seção **Configuração Básica de SAML** no portal do Azure.

    g. Clique em **Save** (Salvar).

### <a name="create-askyourteam-test-user"></a>Criar um usuário de teste do AskYourTeam

1. Em outra janela do navegador da Web, entre no site do AskYourTeam como administrador.

1. Clique em **Minha Organização**.

    ![Captura de tela que mostra o link Minha Organização, em que você inicia esta tarefa.](./media/askyourteam-tutorial/user1.png)

1. Clique em **Usuários** e selecione **Novo Usuário**.

    ![Captura de tela que mostra o link Usuários com Novo Usuário.](./media/askyourteam-tutorial/user2.png)

1. Na seção **Novo Usuário**, execute as seguintes etapas:

    ![Captura de tela que mostra a seção Novo Usuário, em que você insere as informações do usuário.](./media/askyourteam-tutorial/user3.png)

    1. Na caixa de texto **Nome**, insira o nome do usuário.

    1. Na caixa de texto **Sobrenome**, insira o sobrenome do usuário.

    1. Na caixa de texto **Email**, insira o endereço de email do usuário como B.Simon@contoso.com.

    1. Selecione a **Função** para o usuário de acordo com o requisito da sua organização.

    1. Clique em **Save** (Salvar).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do AskYourTeam, na qual poderá iniciar o fluxo de logon.

* Acesse diretamente a URL de Logon do AskYourTeam e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao AskYourTeam para o qual configurou o SSO

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do AskYourTeam em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao AskYourTeam para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o AskYourTeam, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
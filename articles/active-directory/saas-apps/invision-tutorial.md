---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao InVision | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o InVision.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: ec35917ca18064d58279d8ed2b3fb1f0e83a88fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736927"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao InVision

Neste tutorial, você aprenderá a integrar o InVision ao Azure AD (Azure Active Directory). Quando integrar o InVision ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao InVision.
* Permitir que os usuários sejam conectados automaticamente ao InVision com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do InVision com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O InVision dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-invision-from-the-gallery"></a>Como adicionar o InVision por meio da galeria

Para configurar a integração do InVision ao Azure AD, você precisa adicionar o InVision por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **InVision** na caixa de pesquisa.
1. Escolha **InVision** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-invision"></a>Configurar e testar o SSO do Azure AD para o InVision

Configure e teste o SSO do Azure AD com o InVision usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do InVision.

Para configurar e testar o SSO do Azure AD com o InVision, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do InVision](#configure-invision-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do InVision](#create-invision-test-user)** – para ter um equivalente de B.Fernandes no InVision vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **InVision**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.invisionapp.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do InVision](mailto:support@invisionapp.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o InVision**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao InVision.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **InVision**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-invision-sso"></a>Configurar o SSO do InVision

1. Para automatizar a configuração no InVision, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar InVision** e você será direcionado ao aplicativo InVision. Nele, forneça as credenciais de administrador para entrar no InVision. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o InVision manualmente, em uma janela diferente do navegador da Web, entre no site da empresa do InVision como administrador.

1. Clique em **Equipe** e selecione **Configurações**.

    ![A captura de tela mostra a guia Equipe com a opção Configurações selecionada.](./media/invision-tutorial/config1.png)

1. Role para baixo até **Logon único** e clique em **Alterar**.

    ![A captura de tela mostra o botão Alterar para Logon único.](./media/invision-tutorial/config3.png)

1. Na página **Logon único**, execute as seguintes etapas:

    ![A captura de tela mostra a página Logon único em que você insere os valores nesta etapa.](./media/invision-tutorial/config4.png)

    a. Altere **Exigir SSO para cada membro de <nome da conta>** para **Ativado**.

    b. Na caixa de texto **nome**, digite o nome, por exemplo, `azureadsso`.

    c. Insira o valor da URL de logon na caixa de texto **URL de entrada**.

    d. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    e. Na caixa de texto **Certificado SAML**, abra o **Certificado (Base64)** baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto Certificado SAML.

    f. Na caixa de texto **Formato da ID de Nome**, use `Unspecified` para o **Formato de ID de Nome**.

    g. Selecione **SHA-256** na lista suspensa para o **Algoritmo de HASH**.

    h. Insira o nome apropriado para o **Rótulo do Botão de SSO**.

    i. Ative a opção **Permitir o provisionamento just-in-time**.

    j. Clique em **Atualizar**.

### <a name="create-invision-test-user"></a>Criar usuário de teste do InVision

1. Em outra janela do navegador da Web, entre no site do InVision como administrador.

1. Clique em **Equipe** e selecione **Pessoas**.

    ![A captura de tela mostra a guia Equipe com a opção Pessoas selecionada.](./media/invision-tutorial/config2.png)

1. Clique no **+ ÍCONE** para adicionar um novo usuário.

    ![A captura de tela mostra o ícone + para adicionar um usuário.](./media/invision-tutorial/user1.png)

1. Insira o endereço de email do usuário e clique em **Avançar**.

    ![A captura de tela mostra a caixa de diálogo Convidar para em que você pode inserir os endereços.](./media/invision-tutorial/user2.png)

1. Verifique o endereço de email e depois clique em **Convidar**.

    ![A captura de tela mostra a caixa de diálogo Convidar em que você pode selecionar Convidar para continuar.](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do InVision, na qual poderá iniciar o fluxo de logon.

* Acesse a URL de Logon do InVision diretamente e inicie dela o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no InVision para o qual configurou o SSO

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do InVision, em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao InVision para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o InVision, você poderá impor controles de sessão, o que protege o vazamento e a infiltração de dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
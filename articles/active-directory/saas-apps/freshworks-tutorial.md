---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Freshworks | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Freshworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 64a8907ec359235c2856b1f92388d9daa38cd190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651715"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Freshworks

Neste tutorial, você aprenderá a integrar o Freshworks ao Azure AD (Azure Active Directory). Quando integrar o Freshworks ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Freshworks.
* Permitir que os usuários sejam conectados automaticamente ao Freshworks com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Freshworks com SSO (logon único) habilitado.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Freshworks dá suporte ao SSO iniciado por **SP e IdP**

## <a name="add-freshworks-from-the-gallery"></a>Adicionar o Freshworks por meio da galeria

Para configurar a integração do Freshworks ao Azure AD, é necessário adicionar o Freshworks da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Freshworks** na caixa de pesquisa.
1. Selecione **Freshworks** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-freshworks"></a>Configurar e testar o SSO do Azure AD com o Freshworks

Configure e teste o SSO do Azure AD com o Freshworks usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Freshworks.

Para configurar e testar o SSO do Azure AD com o Freshworks, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Freshworks](#configure-freshworks-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Freshworks](#create-freshworks-test-user)** – para ter um equivalente de B.Fernandes no Freshworks que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Freshworks**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/CUSTOM_URL`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.freshworks.com/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do cliente Freshworks](mailto:support@freshworks.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Para modificar as opções de **Autenticação** de acordo com seus requisitos, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

     ![image](common/edit-certificate.png)

     ![Captura de tela que mostra a caixa de diálogo "Certificado de Autenticação SAML" com o botão "Editar" selecionado.](./media/freshworks-tutorial/response.png)

    a. Selecione **Assinar resposta SAML** como **Opção de Assinatura**.

    b. Clique em **Save** (Salvar).

1. Na seção **Configurar o Freshworks**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Freshworks.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Freshworks**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-freshworks-sso"></a>Configurar o SSO do Freshworks

1. Abra uma nova janela do navegador da Web e entre no site da empresa Freshworks como administrador e siga estas etapas:

2. No lado esquerdo do menu, clique no ícone **Segurança**, marque a opção **Logon único** e selecione **SSO SAML** em **Métodos de Autenticação**.

    ![Captura de tela que mostra a seção "Segurança – Métodos de Autenticação" com a opção "Logon único" ativada e "SSO SAML" selecionado.](./media/freshworks-tutorial/configure01.png)

3. Na seção **Logon único**, siga estas etapas:

    ![Configuração do Freshworks](./media/freshworks-tutorial/configure02.png)

    a. Clique em **Copiar** para copiar a **ID da Entidade do SP (Provedor de Serviços)** para sua instância e cole-a na caixa de texto **Identificador (ID da Entidade)** na seção **Configuração SAML Básica** no portal do Azure.

    b. Na caixa de texto **ID da Entidade fornecida pelo IdP**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de SSO SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Abra o certificado codificado em Base64 no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Certificado de segurança**.

    e. Clique em **Save** (Salvar).

### <a name="create-freshworks-test-user"></a>Criar usuário de teste do Freshworks

Nesta seção, você criará um usuário chamado B.Fernandes no Freshworks. Trabalhe com a [equipe de suporte do cliente Freshworks](mailto:support@freshworks.com) para adicionar usuários na plataforma do Freshworks. Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Freshworks, na qual será possível iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Freshworks e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no Freshworks para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Freshworks no portal Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Freshworks para o qual o SSO foi configurado. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Freshworks, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
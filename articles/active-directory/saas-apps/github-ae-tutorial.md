---
title: 'Tutorial: integração do SSO (logon único) do Azure Active Directory com o GitHub AE | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o GitHub AE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 537378ef11333bd8942a61ab2bbb4e8a251c2b59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103196854"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-ae"></a>Tutorial: integração do SSO (logon único) do Azure Active Directory com o GitHub AE

Neste tutorial, você aprenderá como integrar o GitHub AE ao Azure AD (Azure Active Directory). Quando você integrar o GitHub AE ao Azure AD, será possível:

* Controlar quem tem acesso ao GitHub AE no Azure AD.
* Permitir que usuários sejam conectados de modo automático ao GitHub AE usando contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Ter o GitHub AE pronto para a [inicialização](https://docs.github.com/github-ae@latest/admin/configuration/initializing-github-ae).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O GitHub AE é compatível com SSO iniciado por **SP** e **IDP**
* O GitHub AE é compatível com o provisionamento de usuário do **Just-In-Time**

## <a name="adding-github-ae-from-the-gallery"></a>Como adicionar o GitHub AE da galeria

Para configurar a integração do GitHub AE com o Azure AD, será necessário adicionar à sua lista de aplicativos SaaS gerenciados o GitHub AE da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **GitHub AE** na caixa de pesquisa.
1. Selecione **GitHub AE** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-github-ae"></a>Configurar e testar o SSO do Azure AD para o GitHub AE

Configure e teste o SSO do Azure AD usando o GitHub AE com um usuário de teste chamado **B. Simon**. É necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente no GitHub AE para que o SSO funcione.

Conclua os seguintes blocos de construção para configurar e testar o SSO do Azure AD com o GitHub AE:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do GitHub AE](#configure-github-ae-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do GitHub AE](#create-github-ae-test-user)** – para ter um equivalente de B.Simon no GitHub AE que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **GitHub AE**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<YOUR-GITHUB-AE-HOSTNAME>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YOUR-GITHUB-AE-HOSTNAME>/saml/consume`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

     Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<YOUR-GITHUB-AE-HOSTNAME>/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, a URL de Resposta e o Identificador reais. Entre em contato com a [equipe de suporte ao Cliente do GitHub AE](mailto:support@github.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.


1. O aplicativo GitHub AE espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Edite **Declarações e Atributos do Usuário**.

1. Clique em **Adicionar nova declaração** e insira o nome como **administrador** na caixa de texto.

1. Expanda **Condições de declaração** e selecione **Membros** em **Tipo de usuário**.

1. Clique em **Selecionar grupos** e pesquise o **Grupo** que você deseja incluir nessa declaração, cujos membros devem ser administradores do GHAE.

1. Selecione **Atributo** para **Origem** e insira **true** (sem aspas) para o **Valor**. 

1. Clique em **Save** (Salvar).

    ![gerenciar declaração](./media/github-ae-tutorial/administrator.png)

    > [!NOTE]
    > Para conhecer as instruções sobre como adicionar uma declaração, siga o [link](https://docs.github.com/en/github-ae@latest/admin/authentication/configuring-authentication-and-provisioning-for-your-enterprise-using-azure-ad).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o GitHub AE**, copie a(s) URL(s) apropriada(s) de acordo com seus requisitos.

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

Nesta seção, você permitirá que B.Simon use o logon único do Azure, concedendo acesso ao GitHub AE.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **GitHub AE**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-github-ae-sso"></a>Configurar o SSO do GitHub AE

Para configurar o SSO no lado do GitHub AE, será necessário seguir as instruções mencionadas [aqui](https://docs.github.com/github-ae@latest/admin/authentication/configuring-saml-single-sign-on-for-your-enterprise#enabling-saml-sso).

### <a name="create-github-ae-test-user"></a>Criar um usuário de teste do GitHub AE

Nesta seção, um usuário chamado B.Simon será criado no GitHub AE. O GitHub AE é compatível com o provisionamento de usuário do just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um usuário será criado após a autenticação, caso ele ainda não exista no GitHub AE.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado para a URL de Entrada do GitHub AE, onde poderá iniciar o fluxo de logon.  

* Acesse a URL de Entrada do GitHub AE diretamente e inicie o fluxo de logon desse local.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado de modo automático ao GitHub AE para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco GitHub AE do Painel de Acesso, caso o modo SP esteja configurado, você será redirecionado para a página de entrada do aplicativo a fim de iniciar o fluxo de logon. Caso o modo IDP esteja configurado, você será conectado de modo automático ao GitHub AE para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

* [Como configurar o provisionamento de usuário de sua empresa](https://docs.github.com/github-ae@latest/admin/authentication/configuring-user-provisioning-for-your-enterprise).

* Após configurar o GitHub AE, será possível impor o controle de sessão, que protegerá contra a exfiltração e a infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

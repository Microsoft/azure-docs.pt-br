---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao KnowledgeOwl | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o KnowledgeOwl.
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
ms.openlocfilehash: c1c53da8c62acc1b4aacd2b0fa79dd968c65c13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645629"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao KnowledgeOwl

Neste tutorial, você aprenderá a integrar o KnowledgeOwl ao Azure AD (Azure Active Directory). Ao integrar o KnowledgeOwl ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao KnowledgeOwl.
* Permitir que os usuários sejam conectados automaticamente ao KnowledgeOwl com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do KnowledgeOwl.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O KnowledgeOwl dá suporte ao SSO iniciado por **SP e IdP**.
* O KnowledgeOwl dá suporte ao provisionamento de usuário **Just-In-Time**.

## <a name="add-knowledgeowl-from-the-gallery"></a>Adicionar o KnowledgeOwl por meio da galeria

Para configurar a integração do KnowledgeOwl ao Azure AD, você precisará adicionar o KnowledgeOwl da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **KnowledgeOwl** na caixa de pesquisa.
1. Selecione **KnowledgeOwl** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-knowledgeowl"></a>Configurar e testar o SSO do Azure AD para o KnowledgeOwl

Configure e teste o SSO do Azure AD com o KnowledgeOwl usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do KnowledgeOwl.

Para configurar e testar o SSO do Azure AD com o KnowledgeOwl, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do KnowledgeOwl](#configure-knowledgeowl-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do KnowledgeOwl](#create-knowledgeowl-test-user)** : para ter um equivalente de B. Fernandes no KnowledgeOwl que esteja vinculado à representação de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **KnowledgeOwl**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite a URL usando um dos seguintes padrões:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. Na caixa de texto **URL de Resposta**, digite a URL nos seguintes padrões:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL usando um dos seguintes padrões:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Esses valores não são reais. Você precisará atualizar esse valor do Identificador, das URLs de resposta e da URL de entrada reais que são explicados posteriormente no tutorial.

1. O aplicativo KnowledgeOwl espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo KnowledgeOwl espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem | Namespace |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Bruto)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificateraw.png)

1. Na seção **Configurar o KnowledgeOwl**, copie a(s) URL(s) apropriada(s) de acordo com seus requisitos.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao KnowledgeOwl.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **KnowledgeOwl**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-knowledgeowl-sso"></a>Configurar o SSO do KnowledgeOwl

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do KnowledgeOwl como administrador.

1. Clique em **Configurações** e, em seguida, selecione **Segurança**.

    ![A captura de tela mostra a opção Segurança selecionada no menu Configurações.](./media/knowledgeowl-tutorial/configure-1.png)

1. Role até a **Integração de SSO do SAML** e execute as seguintes etapas:

    ![A captura de tela mostra a seção Integração de SSO do SAML em que você pode fazer as alterações descritas aqui.](./media/knowledgeowl-tutorial/configure-2.png)

    a. Selecione **Habilitar SSO do SAML**.

    b. Copie o valor da **ID da Entidade SP** e cole-o na caixa de texto **Identificador (ID da Entidade)** na seção **Configuração Básica de SAML** no portal do Azure.

    c. Copie o valor da **URL de Logon SP** e cole-o nas caixas de texto **URL de Logon e URL de Resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    d. Na caixa de texto **ID da entidade de IdP**, cole o valor do **Identificador do Microsoft Azure Active Directory** copiado do portal do Azure.

    e. Na caixa de texto **URL de Logon de IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    f. Na caixa de texto **URL de Logoff do IdP**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    g. Carregue o formulário de certificado baixado do portal do Azure clicando em **Carregar certificado de IdP**.

    h. Clique em **Mapa de atributos SAML** para mapear atributos e executar as seguintes etapas:

    ![A captura de tela mostra Mapear Atributos SAML em que você pode fazer as alterações descritas aqui.](./media/knowledgeowl-tutorial/configure-3.png)

    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` na caixa de texto **ID do SSO**
    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` na caixa de texto **Email/Nome de usuário**.
    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` na caixa de texto **Nome**.
    * Digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` na caixa de texto **Sobrenome**.
    * Clique em **Salvar**.

    i. Na parte inferior da página, clique em **Salvar** .

    ![Captura de tela que mostra o botão Salvar.](./media/knowledgeowl-tutorial/configure-4.png)

### <a name="create-knowledgeowl-test-user"></a>Criar um usuário de teste do KnowledgeOwl

Nesta seção, um usuário chamado B. Fernandes será criado no KnowledgeOwl. O KnowledgeOwl dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no KnowledgeOwl, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do KnowledgeOwl, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do KnowledgeOwl e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no KnowledgeOwl, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do KnowledgeOwl, em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no KnowledgeOwl, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o KnowledgeOwl, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
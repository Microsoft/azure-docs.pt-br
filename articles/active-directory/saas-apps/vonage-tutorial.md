---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao vonage | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o vonage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e42dc6036d7ad44f1bbb15fc3af28c487ee67640
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733479"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-vonage"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao vonage

Neste tutorial, você aprenderá a integrar o vonage ao Azure AD (Azure Active Directory). Ao integrar o vonage ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao vonage.
* Permitir que os usuários sejam conectados automaticamente ao vonage com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do vonage habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.


* O vonage é compatível com o SSO iniciado por **SP e IdP**

## <a name="adding-vonage-from-the-gallery"></a>Adicionar o vonage por meio da galeria

Para configurar a integração do vonage ao Azure AD, você precisa adicionar o vonage por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **vonage** na caixa de pesquisa.
1. Selecione **vonage** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-vonage"></a>Configurar e testar o SSO do Azure AD para vonage

Configure e teste o SSO do Azure AD com o vonage usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do vonage.

Para configurar e testar o SSO do Azure AD com o vonage, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do vonage](#configure-vonage-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do vonage](#create-vonage-test-user)** – para ter um equivalente de B. Fernandes no vonage que esteja vinculado à declaração do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **vonage**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `wso2is-<ENVIRONMENT>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.auth-<ENVIRONMENT>.vonage.com/accountrecoveryendpoint/saml-translator.jsp?id=<ID>&env=<ENVIRONMENT>&client=Web`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://admin.<ENVIRONMENT>.vocal-<ENVIRONMENT>.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do vonage](mailto:office@vonage.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o vonage**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao vonage.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **vonage**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-vonage-sso"></a>Configurar o SSO do vonage

1. Em outra janela do navegador da Web, entre no site do vonage como administrador.

1. Acesse a **Conta > Configurações de Logon Único > Configurações**.

1. Execute as etapas abaixo na página a seguir.

    ![Página de configurações de logon único](./media/vonage-tutorial/sso-settings.png)

    a. Selecione **Habilitar o logon único para esta conta**.

    b. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de Entrada**, cole o valor da **URL de Logon** que você copiou do portal do Azure. 

    d. Carregue o arquivo **Certificate(Base64)** que você baixou do portal do Azure para **Carregar Certificado**.

### <a name="create-vonage-test-user"></a>Criar um usuário de teste do vonage

1. Em outra janela do navegador da Web, entre no site do vonage como administrador.

1. Acesse **Sistema Telefônico > Usuários > Adicionar Novos**.

    ![página adicionar usuário](./media/vonage-tutorial/add-user.png)

1. Adicione os campos obrigatórios na página a seguir e clique em **Salvar**.

    ![página de formulário adicionar usuário](./media/vonage-tutorial/add-user-2.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do vonage, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do vonage e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você deverá ser conectado automaticamente ao vonage para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do vonage no portal Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao vonage para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o vonage, será possível impor o controle de sessão, que fornece proteção contra a infiltração e exfiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
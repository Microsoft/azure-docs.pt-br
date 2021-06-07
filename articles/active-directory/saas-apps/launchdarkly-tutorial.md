---
title: 'Tutorial: Integração do Azure Active Directory com o LaunchDarkly | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: d9db86e400d862dd67582ede0bf44b9e9fd1c893
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954795"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Tutorial: Integração do Azure Active Directory com o LaunchDarkly

Neste tutorial, você aprenderá a integrar o LaunchDarkly ao Azure AD (Azure Active Directory). Quando integrar o LaunchDarkly ao Azure AD, você poderá:

* Controlar quem tem acesso ao LaunchDarkly no Azure AD.
* Permitir que seus usuários entrem automaticamente no LaunchDarkly com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

    > [!NOTE]
    > A integração do Azure Active Directory do LaunchDarkly é unidirecional. Depois de configurar a integração, você poderá usar o Azure AD para gerenciar usuários, SSO e contas no LaunchDarkly, mas **não pode** usar o LaunchDarkly para gerenciar usuários, SSO e contas no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único do LaunchDarkly.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O LaunchDarkly dá suporte ao SSO iniciado por **IDP**.
* O LaunchDarkly dá suporte ao provisionamento de usuário **Just In Time**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-launchdarkly-from-the-gallery"></a>Adicionar o LaunchDarkly da galeria

Para configurar a integração do LaunchDarkly ao Azure AD, você precisa adicionar o LaunchDarkly da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **LaunchDarkly** na caixa de pesquisa.
1. Selecione **LaunchDarkly** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Configurar e testar o SSO do Azure AD para LaunchDarkly

Configure e teste o SSO do Azure AD com o LaunchDarkly usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LaunchDarkly.

Para configurar e testar o SSO do Azure AD com o LaunchDarkly, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do LaunchDarkly](#configure-launchdarkly-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do LaunchDarkly](#create-launchdarkly-test-user)** – para ter um equivalente de B.Fernandes no LaunchDarkly vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **LaunchDarkly**, encontrar a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **Identificador**, digite a URL `app.launchdarkly.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Você atualizará o valor com a URL de Resposta real, que é explicada no tutorial posteriormente. Se estiver pretendendo usar o aplicativo no modo **IDP**, você deverá deixar o campo **URL de Logon** em branco, caso contrário, você não poderá iniciar o logon do **IDP**. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o LaunchDarkly**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao LaunchDarkly.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **LaunchDarkly**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-launchdarkly-sso"></a>Configurar SSO do LaunchDarkly

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do LaunchDarkly como administrador.

2. Selecione **Configurações de conta** do painel de navegação esquerdo.

    ![A captura de tela mostra o item Configurações de Conta selecionado em Produção.](./media/launchdarkly-tutorial/configure-1.png)

3. Clique na guia **Segurança**.

    ![A captura de tela mostra a guia Segurança das Configurações de conta.](./media/launchdarkly-tutorial/configure-2.png)

4. Clique em **Habilitar SSO** e **Editar configuração de SAML**.

    ![A captura de tela mostra a página Logon único em que você pode HABILITAR O SSO e EDITAR A CONFIGURAÇÃO DO SAML.](./media/launchdarkly-tutorial/configure-3.png)

5. Na seção **Editar sua configuração do SAML**, realize as seguintes etapas:

    ![A captura de tela mostra a seção Editar sua configuração do SAML em que você pode fazer as alterações descritas aqui.](./media/launchdarkly-tutorial/configure-4.png)

    a. Copie a **URL de serviço do consumidor do SAML** para a sua instância e cole-a na caixa de texto URL de Resposta na seção **Domínio e URLs do LaunchDarkly** no portal do Azure.

    b. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    c. Abra o certificado baixado do portal do Azure no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa **certificado x. 509** ou você pode carregar diretamente o certificado clicando em **Carregar um**.

    d. Clique em **Save** (Salvar).

### <a name="create-launchdarkly-test-user"></a>Criar um usuário de teste do LaunchDarkly

Nesta seção, um usuário chamado B.Fernandes será criado no LaunchDarkly. O LaunchDarkly é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no LaunchDarkly, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e entre automaticamente no LaunchDarkly para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do LaunchDarkly nos Meus Aplicativos, você deverá ser conectado automaticamente ao LaunchDarkly no qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o LaunchDarkly, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
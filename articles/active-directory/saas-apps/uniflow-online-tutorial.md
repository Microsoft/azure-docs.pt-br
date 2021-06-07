---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao uniFLOW Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o uniFLOW Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9fdcd8a82b901e00e28f0ddd89ba53d9a2e3fbae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952663"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao uniFLOW Online

Neste tutorial, você aprenderá a integrar o uniFLOW Online ao Azure AD (Azure Active Directory). Quando integrar o uniFLOW Online ao Azure AD, será possível:

* Controlar no Azure AD quem tem acesso ao uniFLOW Online.
* Permitir que os usuários entrem automaticamente no uniFLOW Online com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Locatário do uniFLOW Online.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O uniFLOW Online é compatível com o SSO iniciado por **SP**

## <a name="add-uniflow-online-from-the-gallery"></a>Adicionar o uniFLOW Online da galeria

Para configurar a integração do uniFLOW Online ao Azure AD, você precisa adicionar o uniFLOW Online da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **uniFLOW Online** na caixa de pesquisa.
1. Selecione **uniFLOW Online** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Configurar e testar o SSO do Azure AD para o uniFLOW Online

Configure e teste o SSO do Azure AD com o uniFLOW Online usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do uniFLOW Online.

Para configurar e testar o SSO do Azure AD com o uniFLOW Online, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
   1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
   1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do uniFLOW Online](#configure-uniflow-online-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Entrar no uniFLOW Online usando o usuário de teste criado](#sign-in-to-uniflow-online-using-the-created-test-user)** : para testar a conexão do usuário no lado do aplicativo.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **uniFLOW Online**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando um dos seguintes padrões:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando um dos seguintes padrões:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre com contato com a [equipe de suporte ao Cliente do uniFLOW Online](mailto:support@nt-ware.com) para obter esses valores. Veja também os padrões mostrados na seção **Configuração Básica do SAML** do portal do Azure ou a URL de resposta exibida em seu locatário do uniFLOW Online.

1. Seu aplicativo uniFLOW Online espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo uniFLOW Online espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de Origem|
    | -----------| --------------- |
    | displayname | user.displayname |
    | apelido | user.onpremisessamaccountname |

   > [!NOTE]
   > O atributo `user.onpremisessamaccountname` só conterá um valor se os usuários do Azure AD forem sincronizados de um Microsoft Active Directory local.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao uniFLOW Online.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **uniFLOW Online**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

> [!NOTE]
> Para permitir que todos os usuários acessem o aplicativo sem atribuição manual, acesse a seção **Gerenciar** e selecione **Propriedades**. Em seguida, altere o parâmetro **Atribuição de usuário necessária** para **NÃO**.

## <a name="configure-uniflow-online-sso"></a>Configurar o SSO do uniFLOW Online

1. Em uma janela do navegador da Web diferente, entre no site uniFLOW Online como administrador.

1. No painel de navegação esquerdo, selecione a guia **Usuário**.

    ![A captura de tela mostra a opção Usuários selecionada no site do uniflow Online.](./media/uniflow-online-tutorial/configure-1.png)

1. Clique em **Provedores de Identidade**.

    ![A captura de tela mostra a opção Provedores de Identidade selecionada.](./media/uniflow-online-tutorial/configure-2.png)

1. Clique em **Adicionar provedor de identidade**.

    ![A captura de tela mostra a opção Adicionar provedor de Identidade selecionada.](./media/uniflow-online-tutorial/configure-3.png)

1. Na seção **ADICIONAR PROVEDOR DE IDENTIDADE**, execute as seguintes etapas:

    ![A captura de tela mostra a seção ADICIONAR PROVEDOR DE IDENTIDADE, na qual você pode inserir os valores descritos.](./media/uniflow-online-tutorial/configure-4.png)

    a. Insira o nome de exibição Por ex: *SSO do AzureAD*.

    b. Para **Tipo de provedor**, selecione a opção **WS-Fed** no menu suspenso.

    c. Para **Tipo WS-Fed**, selecione a opção **Azure Active Directory** no menu suspenso.

    d. Clique em **Save** (Salvar).

1. Na guia **Geral**, execute as seguintes etapas:

    ![A captura de tela mostra a guia Geral, em que você pode inserir os valores descritos.](./media/uniflow-online-tutorial/configure-5.png)

    a. Insira o nome de exibição Por ex: *SSO do AzureAD*.

    b. Selecione a opção **De uma URL** para **Metadados de Federação do ADFS**.

    c. Na caixa de texto **URL de Metadados de Federação**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do portal do Azure.

    d. Selecione **Provedor de identidade** como **Habilitado**.

    e. Selecione **Registro de usuário automático** como **Ativado**.

    f. Clique em **Save** (Salvar).

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Entrar no uniFLOW Online usando o usuário de teste criado

1. Em outra janela do navegador da Web, acesse a URL do uniFLOW Online de seu locatário.

1. Selecione o provedor de identidade criado anteriormente para entrar usando sua instância do Azure AD.

1. Entre usando o usuário de teste.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do uniFLOW Online, na qual será possível iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do uniFLOW Online e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do uniFLOW Online em Meus Aplicativos, será redirecionado à URL de logon do uniFLOW Online. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o uniFLOW Online, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração de dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
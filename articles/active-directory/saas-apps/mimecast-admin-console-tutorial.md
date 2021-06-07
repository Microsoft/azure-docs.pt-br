---
title: 'Tutorial: Integração do Azure Active Directory ao Mimecast Admin Console | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mimecast Admin Console.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 606665cead215c049e55f22a5f8ff0f668181fb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647176"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Mimecast Admin Console

Neste tutorial, você aprenderá a integrar o Mimecast Admin Console ao Azure AD (Azure Active Directory). Ao integrar o Mimecast Admin Console ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Mimecast Admin Console.
* Permitir que os usuários sejam conectados automaticamente ao Mimecast Admin Console com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Mimecast Admin Console com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Mimecast Admin Console é compatível com SSO iniciado por **SP e IDP**

## <a name="add-mimecast-admin-console-from-the-gallery"></a>Adicionar o Mimecast Admin Console por meio da galeria

Para configurar a integração do Mimecast Admin Console com o Azure AD, você precisará adicionar o Mimecast Admin Console à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Mimecast Admin Console** na caixa de pesquisa.
1. Selecione **Mimecast Admin Console** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-mimecast-admin-console"></a>Configurar e testar o SSO do Azure AD para o Mimecast Admin Console

Configure e teste o SSO do Azure AD com o Mimecast Admin Console usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Mimecast Admin Console.

Para configurar e testar o SSO do Azure AD com o Mimecast Admin Console, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Mimecast Admin Console](#configure-mimecast-admin-console-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Mimecast Admin Console](#create-mimecast-admin-console-test-user)** – para ter um equivalente de B.Fernandes no Mimecast Admin Console que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Mimecast Admin Console**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por IDP, execute as seguintes etapas:

    a. Na caixa de texto **Identificador**, digite a URL no seguinte padrão: 

    | Região  |  Valor | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | África do Sul    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Exterior        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Você encontrará o valor `accountcode` no Mimecast Admin Console em **Conta** > **Configurações** > **Código de Conta**. Acrescente o `accountcode` ao identificador.

    b. Na caixa de texto **URL de Resposta**, digite a URL:  

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Exterior        | `https://jer-api.mimecast.com/login/saml`|

1. Se quiser configurar o aplicativo no modo iniciado em **SP**:

    Na caixa de texto **URL de Logon**, digite a URL:  

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | Estados Unidos   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | África do Sul    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | Austrália       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | Exterior        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Mimecast Admin Console.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Mimecast Admin Console**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-mimecast-admin-console-sso"></a>Configurar o SSO do Mimecast Admin Console

1. Em outra janela do navegador da Web, entre no Mimecast Admin Console.

1. Navegue até **Administração** > **Serviços** > **Aplicativos**.

    ![A captura de tela mostra a janela Mimecast com a opção Aplicativos selecionada.](./media/mimecast-admin-console-tutorial/services.png)

1. Clique na guia **Perfis de Autenticação**.
    
    ![A captura de tela mostra a guia Aplicativo com a opção Perfis de Autenticação selecionada.](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. Clique na guia **Novo Perfil de Autenticação**.

    ![A captura de tela mostra a opção Novo Perfil de Autenticação selecionada.](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. Forneça uma descrição válida na caixa de texto **Descrição** e marque a caixa de seleção **Impor Autenticação SAML para o Console de Administração**.

    ![A captura de tela mostra onde selecionar Impor Autenticação SAML para o Console de Administração.](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. Na página **Configuração do SAML para o Console de Administração**, realize as seguintes etapas:

    ![A captura de tela mostra a página Configuração do SAML para o Console de Administração em que você pode inserir os valores descritos.](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. Para **Provedor**, selecione **Azure Active Directory** na lista suspensa.

    b. Na caixa de texto **URL de Metadados**, cole o valor da **URL de Metadados de Federação do Aplicativo** que você copiou do portal do Azure.

    c. Clique em **Importar**. Depois de importar a URL de metadados, os campos serão preenchidos automaticamente, sem a necessidade de executar alguma ação nesses campos.

    d. Desmarque as caixas de seleção **Usar o Contexto Protegido por Senha** e **Usar o Contexto de Autenticação Integrada**.

    e. Clique em **Save** (Salvar).

### <a name="create-mimecast-admin-console-test-user"></a>Criar um usuário de teste do Mimecast Admin Console

1. Em outra janela do navegador da Web, entre no Mimecast Admin Console.

1. Navegue até **Administração** > **Diretórios** > **Diretórios Internos**.

    ![A captura de tela mostra a janela Mimecast com a opção Diretórios Internos selecionada.](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. Selecione em seu domínio, se o domínio for mencionado abaixo, caso contrário, crie um domínio clicando em **Novo Domínio**.

    ![A captura de tela mostra o domínio selecionado.](./media/mimecast-admin-console-tutorial/domain-name.png)

1. Clique na guia **Novo Endereço**.

    ![A captura de tela mostra a guia Novo Endereço selecionada.](./media/mimecast-admin-console-tutorial/new-address.png)

1. Forneça as informações de usuário necessárias na seguinte página:

    ![A captura de tela mostra a página em que você pode inserir os valores descritos.](./media/mimecast-admin-console-tutorial/user-information.png)

    a. Na caixa de texto **Endereço de Email**, insira o endereço de email do usuário como `B.Simon@yourdomainname.com`.

    b. Na caixa de texto **Nome Global**, insira o **Nome completo** do usuário.

    c. Nas caixas de texto **Senha** e **Confirmar Senha**, digite a senha do usuário.

    d. Marque a caixa de seleção **Forçar Alteração no Logon**.

    e. Clique em **Save** (Salvar).

    f. Para atribuir funções ao usuário, clique em **Editar Função** e atribua a função necessária ao usuário de acordo com o requisito da sua organização.

    ![A captura de tela mostra as Configurações de Endereço em que você pode selecionar Edição de Função.](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Mimecast Admin Console, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Mimecast Admin Console e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Mimecast Admin Console, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Mimecast Admin Console em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Mimecast Admin Console, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Mimecast Admin Console, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
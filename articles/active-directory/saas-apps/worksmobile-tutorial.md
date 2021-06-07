---
title: 'Tutorial: Integração do Azure Active Directory ao LINE WORKS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LINE WORKS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: jeedes
ms.openlocfilehash: a8b344cd9e4428c3508534edaec4fd98fb67d832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182029"
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Tutorial: Integração do Azure Active Directory ao LINE WORKS

Neste tutorial, você aprende a integrar o LINE WORKS ao Azure Active Directory (Azure AD).
A integração do LINE WORKS ao Azure AD proporciona os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao LINE WORKS.
* É possível permitir que os usuários entrem automaticamente no LINE WORKS (Logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao LINE WORKS, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do LINE WORKS com logon único habilitado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O LINE WORKS é compatível com o SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-line-works-from-the-gallery"></a>Adicionar o LINE WORKS da galeria

Para configurar a integração do LINE WORKS ao Azure AD, você precisará adicionar o LINE WORKS da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **LINE WORKS** na caixa de pesquisa.
1. Selecione **LINE WORKS** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o LINE WORKS usando uma usuária de teste chamada **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LINE WORKS.

Para configurar e testar o SSO do Azure AD com o LINE WORKS, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do LINE WORKS](#configure-line-works-sso)** – Para definir as configurações de Logon Único no lado do aplicativo.
    * **[Criar um usuário de teste do LINE WORKS](#create-line-works-test-user)** – para ter um equivalente de Brenda Fernandes no LINE WORKS vinculado à representação do usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **LINE WORKS**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://auth.worksmobile.com/d/login/<domain>/`

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://auth.worksmobile.com/acs/ <domain>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon propriamente ditas. Entre em contato com a [equipe de suporte do LINE WORKS](https://line.worksmobile.com/jp/en/contactus/) para obter os valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar LINE WORKS**, copie as URLs apropriadas, de acordo com seus requisitos.

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

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure ao permitir a ela o acesso ao LINE WORKS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **LINE WORKS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-line-works-sso"></a>Configurar o SSO do LINE WORKS

Para configurar o logon único no **LINE WORKS**, leia os [documentos de SSO do LINE WORKS](https://developers.worksmobile.com/jp/document/1001080101) e defina uma configuração do LINE WORKS.

> [!NOTE]
> É necessário converter o arquivo de certificado baixado de .cert para .pem


### <a name="create-line-works-test-user"></a>Criar um usuário de teste do LINE WORKS

Nesta seção, você criará uma usuária chamada Brenda Fernandes no LINE WORKS. Acesse a [página de administração do LINE WORKS](https://admin.worksmobile.com) e adicione os usuários na plataforma do LINE WORKS.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do LINE WORKS, na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de Logon do LINE WORKS diretamente e inicie dela o fluxo de logon.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco LINE WORKS no Painel de Acesso, você será redirecionado para a URL de Logon do LINE WORKS. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o LINE WORKS, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: integração do Azure Active Directory ao Expensify | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Expensify.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 5c68453f7f7d638877683920a17022cf3d29bdc5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597798"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Tutorial: integrar o Expensify ao Azure Active Directory

Neste tutorial, você aprenderá como integrar o Expensify ao Azure Active Directory (Azure AD). Ao integrar o Expensify ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Expensify.
* Permitir que seus usuários entrem automaticamente no Expensify com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Expensify habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Expensify é compatível com o SSO iniciado por **SP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-expensify-from-the-gallery"></a>Adicionar o Expensify por meio da galeria

Para configurar a integração do Expensify ao Azure AD, você precisa adicionar o Expensify da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Expensify** na caixa de pesquisa.
1. Selecione **Expensify** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-expensify"></a>Configurar e testar o SSO do Azure AD para o Expensify

Configure e teste o SSO do Azure AD com o Expensify usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Expensify.

Para configurar o SSO do Azure AD para o Expensify, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Expensify](#configure-expensify-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Expensify](#create-expensify-test-user)** – para ter um equivalente de B.Fernandes no Expensify que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Expensify**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://www.expensify.com/authentication/saml/login`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://www.expensify.com`

    c. b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize esse valor com a URL de Resposta real. Contate a [equipe de suporte ao Cliente do Expensify](mailto:help@expensify.com) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Assinatura SAML**, localize **XML de Metadados** e selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Expensify**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao Expensify.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Expensify**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-expensify-sso"></a>Configurar o SSO do Expensify

Para habilitar o SSO no Expensify, primeiro habilite o **Controle de Domínio** no aplicativo. Você pode habilitar o Controle de Domínio no aplicativo por meio das etapas listadas [aqui](https://help.expensify.com/domain-control). Para obter suporte adicional, trabalhe com a [equipe de suporte ao Cliente do Expensify](mailto:help@expensify.com). Depois que o Controle de Domínio estiver habilitado, siga estas etapas:

![Configurar o logon único](./media/expensify-tutorial/domain-control.png)

1. Faça logon no seu aplicativo Expensify.

2. No painel esquerdo, clique em **Configurações** e navegue para **SAML**.

3. Alterne a opção **Logon do SAML** para **habilitado**.

4. Abra os Metadados de Federação baixados do Azure AD no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Metadados do Provedor de Identidade**.

### <a name="create-expensify-test-user"></a>Criar usuário de teste do Expensify

Nesta seção, você criará um usuário chamado B.Fernandes no Expensify. Trabalhe com a [equipe de suporte ao Cliente do Expensify](mailto:help@expensify.com) para adicionar usuários na plataforma do Expensify.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Expensify, na qual será possível iniciar o fluxo de logon. 

* Acesse a URL de Logon do Expensify diretamente e inicie o fluxo de logon por meio dela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Expensify em Meus Aplicativos, isso redirecionará você à URL de Logon do Expensify. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o Expensify, você poderá impor o controle de sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
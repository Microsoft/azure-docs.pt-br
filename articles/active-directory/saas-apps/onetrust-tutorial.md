---
title: 'Tutorial: Integração do Azure Active Directory com o software de gerenciamento de privacidade OneTrust | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o software de gerenciamento de privacidade OneTrust.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: f0a145b0eb9dd9dbed0927ce825a21d8f47c48ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648417"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Tutorial: Integração do Azure Active Directory com o software de gerenciamento de privacidade OneTrust

Neste tutorial, você aprenderá a integrar o OneTrust Privacy Management Software ao Azure AD (Azure Active Directory). Ao integrar o OneTrust Privacy Management Software ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao OneTrust Privacy Management Software.
* Permitir que os usuários sejam conectados automaticamente ao OneTrust Privacy Management Software com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o software de gerenciamento de privacidade OneTrust, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Caso não tenha um ambiente do Azure AD, obtenha uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura do OneTrust Privacy Management Software habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O software de gerenciamento de privacidade OneTrust é compatível com SSO iniciado por **SP** e **IDP**

* O software de gerenciamento de privacidade OneTrust é compatível com o provisionamento de usuário **Just In Time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>Adicionar o OneTrust Privacy Management Software por meio da galeria

Para configurar a integração do software de gerenciamento de privacidade OneTrust no Azure AD, você precisa adicionar o software de gerenciamento de privacidade OneTrust da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **OneTrust Privacy Management Software** na caixa de pesquisa.
1. Selecione **OneTrust Privacy Management Software** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>Configurar e testar o SSO do Azure AD para o OneTrust Privacy Management Software

Configure e teste o SSO do Azure AD com o OneTrust Privacy Management Software usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no OneTrust Privacy Management Software.

Para configurar e testar o SSO do Azure AD com o OneTrust Privacy Management Software, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do OneTrust Privacy Management Software](#configure-onetrust-privacy-management-software-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do OneTrust Privacy Management Software](#create-onetrust-privacy-management-software-test-user)** : para ter um equivalente de B.Fernandes no OneTrust Privacy Management Software que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o SSO do Azure AD no portal do Azure.
 
1. No portal do Azure, na página de integração de aplicativos do **OneTrust Privacy Management Software**, localize a seção **Gerenciar** e selecione **Logon Único**.
1. Na página **Selecionar um Método de Logon Único**, selecione **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    a. Na caixa de texto **Identificador**, digite a URL `https://www.onetrust.com/saml2`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

     Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do software de gerenciamento de privacidade OneTrust](mailto:support@onetrust.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o software de gerenciamento de privacidade OneTrust**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.
1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de seleção **Mostrar senha** e, em seguida, anote a senha.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao OneTrust Privacy Management Software.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Software de gerenciamento de privacidade OneTrust**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="configure-onetrust-privacy-management-software-sso"></a>Configurar o SSO do OneTrust Privacy Management Software

Para configurar o logon único no lado do **software de gerenciamento de privacidade OneTrust**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do software de gerenciamento de privacidade OneTrust](mailto:support@onetrust.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Criar usuário de teste do software de gerenciamento de privacidade OneTrust

Nesta seção, um usuário chamado Brenda Fernandes é criado no software de gerenciamento de privacidade OneTrust. O software de gerenciamento de privacidade OneTrust é compatível com o provisionamento Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no software de privacidade OneTrust, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do software de gerenciamento de privacidade OneTrust](mailto:support@onetrust.com).

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do OneTrust Privacy Management Software, na qual poderá iniciar o fluxo de logon.  
 
* Acesse diretamente a URL de Logon do OneTrust Privacy Management Software e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao OneTrust Privacy Management Software, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do OneTrust Privacy Management Software em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao OneTrust Privacy Management Software, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o OneTrust Privacy Management Software, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
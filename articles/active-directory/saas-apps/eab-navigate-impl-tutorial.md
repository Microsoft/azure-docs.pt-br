---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EAB Implementation | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EAB Implementation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: jeedes
ms.openlocfilehash: a1f288aaa51585b825cd9a9970c4d179dbfd34da
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223142"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-implementation"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EAB Implementation

Neste tutorial, você aprenderá a integrar o EAB Implementation ao Azure AD (Azure Active Directory). Ao integrar o EAB Implementation ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao EAB Implementation.
* Permitir que os usuários entrem automaticamente no EAB Implementation com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do EAB Implementation habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EAB Implementation dá suporte ao SSO iniciado por **SP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-eab-implementation-from-the-gallery"></a>Como adicionar o EAB Implementation por meio da galeria

Para configurar a integração do EAB Implementation ao Azure AD, você precisa adicionar o EAB Implementation por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **EAB Implementation** na caixa de pesquisa.
1. Selecione **EAB Implementation** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-eab-implementation"></a>Configurar e testar o SSO do Azure AD para o EAB Implementation

Configure e teste o SSO do Azure AD com o EAB Implementation por meio de um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do EAB Implementation.

Para configurar e testar o SSO do Azure AD com o EAB Implementation, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do EAB Implementation](#configure-eab-implementation-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do EAB Implementation](#create-eab-implementation-test-user)** : para ter um equivalente de B.Fernandes no EAB Implementation que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **EAB Implementation**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , insira exatamente o seguinte valor: `https://impl.bouncer.eab.com`
    
    b. Na caixa de texto **URL de Resposta (URL do Serviço do Consumidor de Declaração)** , insira os seguintes valores como linhas separadas: 
    
    | URL de resposta|
    | -------------- |
    | `https://impl.bouncer.eab.com/sso/saml2/acs` |
    | `https://impl.bouncer.eab.com/sso/saml2/acs/` |
    |
    
    c. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.navigate.impl.eab.com/`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do EAB Implementation](mailto:EABTechSupport@eab.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao EAB Implementation.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **EAB Implementation**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-eab-implementation-sso"></a>Configurar o SSO do EAB Implementation

Para configurar o logon único no lado do **EAB Implementation**, é necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do EAB Implementation](mailto:EABTechSupport@eab.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-eab-implementation-test-user"></a>Criar um usuário de teste do EAB Implementation

Nesta seção, você criará um usuário chamado B.Fernandes no EAB Implementation. Trabalhe com a [equipe de suporte do EAB Implementation](mailto:EABTechSupport@eab.com) para adicionar os usuários à plataforma do EAB Implementation. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do EAB Implementation, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do EAB Implementation e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do EAB Implementation em Meus Aplicativos, isso redirecionará você à URL de Logon do EAB Implementation. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o EAB Implementation, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

---
title: 'Tutorial: Integração do Azure Active Directory com o Bynder | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Bynder.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: jeedes
ms.openlocfilehash: 9e233e30521e5c1fa05509fa6dc42a567f6ac4e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653090"
---
# <a name="tutorial-integrate-bynder-with-azure-active-directory"></a>Tutorial: Integrar o Bynder ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Bynder ao Azure AD (Azure Active Directory). Ao integrar o Bynder ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Bynder.
* Permitir que os usuários sejam conectados automaticamente ao Bynder com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Bynder.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Bynder dá suporte ao SSO iniciado por **SP e IDP**
* O Bynder dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="add-bynder-from-the-gallery"></a>Adicionar o Bynder da galeria

Para configurar a integração do Bynder ao Azure AD, você precisará adicionar o Bynder da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Bynder** na caixa de pesquisa.
1. Selecione **Bynder** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o Bynder usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Bynder.

Para configurar e testar o SSO do Azure AD com o Bynder, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
   1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
   1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Bynder](#configure-bynder-sso)** – para definir as configurações de logon único no lado do aplicativo.
   1. **[Criar um usuário de teste do Bynder](#create-bynder-test-user)** – para ter um equivalente de Brenda Fernandes no Bynder que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Bynder**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    
    Para um domínio padrão: `https://<company name>.getbynder.com`
    
    Para um domínio personalizado: `https://<subdomain>.<domain>.com`

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:
    
     Para um domínio padrão: `https://<company name>.getbynder.com/sso/SAML/authenticate/`
    
    Para um domínio personalizado: `https://<subdomain>.<domain>.com/sso/SAML/authenticate/`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:
    
     Para um domínio padrão: `https://<company name>.getbynder.com/login/`
    
     Para um domínio personalizado: ` https://<subdomain>.<domain>.com/login/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do Bynder](https://www.bynder.com/en/support/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Assinatura SAML**, localize **XML de Metadados** e selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Bynder**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Bynder.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Bynder**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-bynder-sso"></a>Configurar o SSO do Bynder

Para configurar o logon único no lado do **Bynder**, é necessário enviar o **XML de Metadados** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Bynder](https://www.bynder.com/en/support/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.


### <a name="create-bynder-test-user"></a>Criar um usuário de teste do Bynder

Nesta seção, um usuário chamado Brenda Fernandes será criado no Bynder. O Bynder dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Bynder, um novo será criado após a autenticação.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Bynder](https://www.bynder.com/en/support/).

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Bynder, em que você pode iniciar o fluxo de logon.  

* Acesse a URL de Logon do Bynder diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Bynder, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Bynder em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Bynder, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Bynder, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
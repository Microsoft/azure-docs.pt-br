---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Olfeo SAAS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAAS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: abadacebac38a2cd777bf71e41452ed583106474
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056679"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-olfeo-saas"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Olfeo SAAS

Neste tutorial, você aprenderá a integrar o Olfeo SAAS ao Azure AD (Azure Active Directory). Quando você integrar o Olfeo SAAS ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Olfeo SAAS.
* Permitir que os usuários sejam conectados automaticamente ao Olfeo SAAS com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Olfeo SAAS.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Olfeo SAAS é compatível com o SSO iniciado por **SP**

## <a name="adding-olfeo-saas-from-the-gallery"></a>Adicionar o Olfeo SAAS da galeria

Para configurar a integração do Olfeo SAAS ao Azure AD, você precisa adicionar o Olfeo SAAS da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Olfeo SAAS** na caixa de pesquisa.
1. Selecione **Olfeo SAAS** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-olfeo-saas"></a>Configurar e testar o SSO do Azure AD para o Olfeo SAAS

Configure e teste o SSO do Azure AD com o Olfeo SAAS usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Olfeo SAAS.

Para configurar e testar o SSO do Azure AD com o Olfeo SAAS, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Olfeo SAAS](#configure-olfeo-saas-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Olfeo SAAS](#create-olfeo-saas-test-user)** – para ter um equivalente de B.Fernandes no Olfeo SAAS que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Olfeo SAAS**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.olfeo.com/api/sso/saml/<ID>/login`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.olfeo.com/api/sso/saml/<ID>/login`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.olfeo.com/api/sso/saml/<ID>/acs`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do Olfeo SAAS](mailto:equipe-rd@olfeo.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Olfeo SAAS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Olfeo SAAS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-olfeo-saas-sso"></a>Configurar o SSO do Olfeo SAAS

Para configurar um logon único no lado do **Olfeo SAAS**, será necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do Olfeo SAAS](mailto:equipe-rd@olfeo.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-olfeo-saas-test-user"></a>Criar um usuário de teste do Olfeo SAAS

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Olfeo SAAS. Trabalhe com a [equipe de suporte do Olfeo SAAS](mailto:equipe-rd@olfeo.com) para adicionar os usuários na plataforma do Olfeo SAAS. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso vai redirecionar você à URL de Logon do Olfeo SAAS na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Olfeo SAAS diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Olfeo SAAS em Meus Aplicativos, isso vai redirecionar você à URL de Logon do Olfeo SAAS. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Olfeo SAAS, você poderá impor controles de sessão, que fornecem proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

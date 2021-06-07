---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o logon único do AppSec Flow | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do AppSec Flow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: baed7607c1d096aafae5c1d163b6b5e0844e8ad6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590368"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appsec-flow-sso"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao logon único do AppSec Flow

Neste tutorial, você aprenderá a integrar o SSO do AppSec Flow ao Azure AD (Azure Active Directory). Ao integrar o SSO do AppSec Flow ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao SSO do AppSec Flow.
* Permitir que seus usuários sejam conectados automaticamente ao SSO do AppSec Flow com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SSO do AppSec Flow.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SSO do AppSec Flow é compatível com SSO iniciado por **IDP**

## <a name="adding-appsec-flow-sso-from-the-gallery"></a>Como adicionar o SSO do AppSec Flow por meio da galeria

Para configurar a integração do SSO do AppSec Flow com o Azure AD, você precisa adicionar o SSO do AppSec Flow da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SSO do AppSec Flow** na caixa de pesquisa.
1. Selecione **SSO do AppSec Flow** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-appsec-flow-sso"></a>Configurar e testar o SSO do Azure AD para o SSO do AppSec Flow

Configure e teste o SSO do Azure AD com o SSO do AppSec Flow usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SSO do AppSec Flow.

Para configurar e testar o SSO do Azure AD com o SSO do AppSec Flow, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do AppSec Flow](#configure-appsec-flow-sso-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SSO do AppSec Flow](#create-appsec-flow-sso-test-user)** – para ter um equivalente de B.Fernandes no SSO do AppSec Flow que esteja vinculado à representação da usuária no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SSO do AppSec Flow**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o SSO do AppSec Flow**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao SSO do AppSec Flow.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **SSO do AppSec Flow**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-appsec-flow-sso-sso"></a>Configurar o SSO do AppSec Flow

Para configurar o logon único no lado do **SSO do AppSec Flow**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do SSO do AppSec Flow](mailto:sre@convisoappsec.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-appsec-flow-sso-test-user"></a>Criar um usuário de teste do SSO do AppSec Flow

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SSO do AppSec Flow. Trabalhe com a [equipe de suporte do SSO do AppSec Flow](mailto:sre@convisoappsec.com) para adicionar usuários na plataforma do SSO do AppSec Flow. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

1. Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao SSO do AppSec Flow para o qual configurou o SSO

1. Use o Painel de Acesso da Microsoft. Quando você clicar no bloco do SSO do AppSec Flow no Painel de Acesso, será conectado automaticamente ao SSO do AppSec Flow para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SSO do AppSec Flow, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
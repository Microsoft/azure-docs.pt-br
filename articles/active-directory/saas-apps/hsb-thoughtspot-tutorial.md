---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao HSB ThoughtSpot | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HSB ThoughtSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: f321e1ec55df796ee3a2b2b169abe00a97bdd28b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hsb-thoughtspot"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao HSB ThoughtSpot

Neste tutorial, você aprenderá a integrar o HSB ThoughtSpot ao Azure AD (Azure Active Directory). Ao integrar o HSB ThoughtSpot ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao HSB ThoughtSpot.
* Permitir que os usuários sejam conectados automaticamente ao HSB ThoughtSpot com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do HSB ThoughtSpot habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O HSB ThoughtSpot dá suporte ao SSO iniciado por **SP**
* O HSB ThoughtSpot dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-hsb-thoughtspot-from-the-gallery"></a>Como adicionar o HSB ThoughtSpot por meio da galeria

Para configurar a integração do HSB ThoughtSpot ao Azure AD, você precisará adicioná-lo da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **HSB ThoughtSpot** na caixa de pesquisa.
1. Selecione **HSB ThoughtSpot** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-hsb-thoughtspot"></a>Configurar e testar o SSO do Azure AD para o HSB ThoughtSpot

Configure e teste o SSO do Azure AD com o HSB ThoughtSpot usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HSB ThoughtSpot.

Para configurar e testar o SSO do Azure AD no HSB ThoughtSpot, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do HSB ThoughtSpot](#configure-hsb-thoughtspot-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do HSB ThoughtSpot](#create-hsb-thoughtspot-test-user)** – Para ter um equivalente de B.Fernandes no HSB ThoughtSpot que esteja vinculado à declaração de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **HSB ThoughtSpot**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite uma das seguintes URLs:

    | URL de logon |
    | ------------- |
    | `https://hsbthoughtspot.mruscloud.com:443` |
    | `https://hsbthoughtspot.mruscloud.com/#/login` |
    |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o HSB ThoughtSpot**, copie as URLs apropriadas de acordo com os seus requisitos.

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

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure permitindo que ela tenha acesso ao HSB ThoughtSpot.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **HSB ThoughtSpot**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-hsb-thoughtspot-sso"></a>Configurar o SSO do HSB ThoughtSpot

Para configurar o logon único no lado do **HSB ThoughtSpot**, é necessário enviar o **XML de metadados de federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do HSB ThoughtSpot](mailto:HSB-BDL-IT-SAPBO-ADMIN@hsb.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-hsb-thoughtspot-test-user"></a>Criar um usuário de teste do HSB ThoughtSpot

Nesta seção, uma usuária chamada Brenda Fernandes será criada no HSB ThoughtSpot. O HSB ThoughtSpot dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no HSB ThoughtSpot, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do HSB ThoughtSpot, na qual será possível iniciar o fluxo de logon. 

* Acesse a URL de Logon do HSB ThoughtSpot diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando clicar no bloco do HSB ThoughtSpot em Meus Aplicativos, você será redirecionado para a URL de Logon do HSB ThoughtSpot. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o HSB ThoughtSpot, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
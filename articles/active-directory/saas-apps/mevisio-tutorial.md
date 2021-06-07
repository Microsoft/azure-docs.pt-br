---
title: 'Tutorial: integração do SSO (logon único) do Azure Active Directory com o Mevisio | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Mevisio.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: jeedes
ms.openlocfilehash: bf85e28a096e1eb0de429a12def4a350971a315a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94363792"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mevisio"></a>Tutorial: integração do SSO (logon único) do Azure Active Directory com o Mevisio

Neste tutorial, você aprenderá a integrar o Mevisio ao Azure AD (Azure Active Directory). Quando você integrar o Mevisio ao Azure AD, será possível:

* Controlar quem tem acesso ao Mevisio no Azure AD.
* Permitir que usuários sejam conectados de modo automático ao Mevisio usando contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Mevisio habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Mevisio é compatível com SSO iniciado por **SP e IDP**
* O Mevisio é compatível com o provisionamento de usuário do **Just-In-Time**


## <a name="adding-mevisio-from-the-gallery"></a>Como adicionar o Mevisio da galeria

Para configurar a integração do Mevisio com o Azure AD, será necessário adicionar à sua lista de aplicativos SaaS gerenciados o Mevisio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Mevisio** na caixa de pesquisa.
1. Selecione **Mevisio** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-mevisio"></a>Configurar e testar o SSO do Azure AD para o Mevisio

Configure e teste o SSO do Azure AD usando o Mevisio com um usuário de teste chamado **B.Simon**. É necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente no Mevisio para que o SSO funcione.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o Mevisio:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Mevisio](#configure-mevisio-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Mevisio](#create-mevisio-test-user)** – para ter um equivalente de B.Simon no Mevisio que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Mevisio**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.mevisio.com/identity/saml2/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.mevisio.com/identity/saml2/login`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.mevisio.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao Cliente do Mevisio](mailto:support@mevisio.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Mevisio espera que as declarações SAML estejam em um formato específico. Isso requer que você adicione um mapeamento de atributos personalizado à configuração de atributos de tokens SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Mevisio espera que mais alguns atributos sejam passados novamente na resposta SAML. Esses atributos serão mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ------------- | --------- |
    | firstName | user.givenname |
    | lastName | user.surname |

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

Nesta seção, você habilitará B.Simon para usar o logon único do Azure, concedendo acesso ao Mevisio.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha o **Mevisio**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-mevisio-sso"></a>Configurar o SSO do MEVISIO

Para configurar um logon único no lado do **Mevisio**, será necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do Mevisio](mailto:support@mevisio.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-mevisio-test-user"></a>Criar um usuário de teste do Mevisio

Nesta seção, uma usuária chamada Britta Simon será criada no Mevisio. O Mevisio é compatível com o provisionamento de usuário do just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um usuário será criado após a autenticação, caso ele ainda não exista no Mevisio.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado para a URL de Entrada do Mevisio, onde poderá iniciar o fluxo de logon.  

* Acesse a URL de Entrada do Mevisio diretamente e inicie o fluxo de logon desse local.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado de modo automático ao Mevisio para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco Mevisio do Painel de Acesso, caso o modo SP esteja configurado, você será redirecionado para a página de entrada do aplicativo a fim de iniciar o fluxo de logon. Caso o modo IDP esteja configurado, você será conectado de modo automático ao Mevisio para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o Mevisio, será possível impor um controle de sessão, que fornecerá uma proteção contra a exfiltração e a infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
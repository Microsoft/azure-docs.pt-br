---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Cequence Application Security Platform   Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Cequence Application Security Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 967ad66434a09252c1b7afa195facee20279f4dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735274"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cequence-application-security-platform"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Cequence Application Security Platform

Neste tutorial, você aprenderá a integrar o Cequence Application Security Platform ao Azure AD (Azure Active Directory). Quando você integrar o Cequence Application Security Platform ao Azure AD, será possível:

* Controlar quem tem acesso ao Cequence Application Security Platform no Azure AD.
* Permitir que usuários sejam conectados de modo automático ao Cequence Application Security Platform usando contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Cequence Application Security Platform.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cequence Application Security Platform é compatível com SSO iniciado por **SP**

* O Cequence Application Security Platform é compatível com o provisionamento de usuário **Just-In-Time**


## <a name="adding-cequence-application-security-platform-from-the-gallery"></a>Como adicionar o Cequence Application Security Platform por meio da galeria

Para configurar a integração do Cequence Application Security Platform ao Azure AD, será necessário adicionar à sua lista de aplicativos SaaS gerenciados o Cequence Application Security Platform por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Cequence Application Security Platform** na caixa de pesquisa.
1. Selecione **Cequence Application Security Platform** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-cequence-application-security-platform"></a>Configurar e testar o SSO do Azure AD para o Cequence Application Security Platform

Configure e teste o SSO do Azure AD usando o Cequence Application Security Platform com um usuário de teste chamado **B.Fernandes**. É necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente no Cequence Application Security Platform para que o SSO funcione.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o Cequence Application Security Platform:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Cequence Application Security Platform](#configure-cequence-application-security-platform-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Cequence Application Security Platform](#create-cequence-application-security-platform-test-user)** – para ter um equivalente de B. Fernandes no Cequence Application Security Platform que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Cequence Application Security Platform**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<CUSTOMERNAME>.s.cequence.cloud`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<CUSTOMERNAME>.s.cequence.cloud:443/saml/metadata`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao Cliente do Cequence Application Security Platform](mailto:support@cequence.ai) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Cequence Application Security Platform espera que as declarações SAML estejam em um formato específico. Isso requer que você adicione um mapeamento de atributos personalizado à configuração de atributos de tokens SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Cequence Application Security Platform espera que mais alguns atributos sejam passados novamente na resposta SAML. Esses atributos serão mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | --------------- | --------- |
    | Agrupar | user.groups |

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure, concedendo-lhe acesso ao Cequence Application Security Platform.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Cequence Application Security Platform**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cequence-application-security-platform-sso"></a>Configurar o SSO do Cequence Application Security Platform

Para configurar um logon único no lado do **Cequence Application Security Platform**, será necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do Cequence Application Security Platform](mailto:support@cequence.ai). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-cequence-application-security-platform-test-user"></a>Criar um usuário de teste do Cequence Application Security Platform

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Cequence Application Security Platform. O Cequence Application Security Platform é compatível com o provisionamento de usuários Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um usuário será criado após a autenticação, caso ele ainda não exista no Cequence Application Security Platform.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado para a URL de Entrada do Cequence Application Security Platform, local em que poderá iniciar o fluxo de logon. 

2. Acesse a URL de Entrada do Cequence Application Security Platform diretamente e inicie o fluxo de logon nesse local.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco Cequence Application Security Platform do Painel de Acesso, você será redirecionado para a URL de Entrada do Cequence Application Security Platform. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o Cequence Application Security Platform, será possível impor o controle de sessão, que protegerá contra a exfiltração e a infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
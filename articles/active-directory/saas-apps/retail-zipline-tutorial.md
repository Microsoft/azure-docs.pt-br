---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Retail Zipline | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Retail Zipline.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: jeedes
ms.openlocfilehash: 50cf98a2646d88360ea9d48d2d84d98ad237a099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514955"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-retail-zipline"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Retail Zipline

Neste tutorial, você aprenderá a integrar o Retail Zipline ao Azure AD (Azure Active Directory). Quando você integra o Retail Zipline ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao Retail Zipline.
* Permitir que os usuários sejam conectados automaticamente ao Retail Zipline com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para logon único (SSO) do Retail Zipline.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Retail Zipline é compatível com o SSO iniciado por **SP e IDP**
* O Retail Zipline é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-retail-zipline-from-the-gallery"></a>Como adicionar o Retail Zipline da galeria

Para configurar a integração do Retail Zipline ao Azure AD, você precisará adicionar o Retail Zipline da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Retail Zipline** na caixa de pesquisa.
1. Selecione **Retail Zipline** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-retail-zipline"></a>Configurar e testar o SSO do Azure AD para o Retail Zipline

Configure e teste o SSO do Azure AD com o Retail Zipline usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Retail Zipline.

Para configurar e testar o SSO do Azure AD com o Retail Zipline, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Retail Zipline](#configure-retail-zipline-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Retail Zipline](#create-retail-zipline-test-user)** – para ter um equivalente de B.Fernandes no Retail Zipline que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Retail Zipline**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.retailzipline.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.retailzipline.com/sso/saml`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.retailzipline.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do cliente do Retail Zipline](mailto:support@retailzipline.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Seu aplicativo Retail Zipline espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributos personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Retail Zipline espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ----------------- | --------- |
    | employee_number | user.employeeid |
    | email | user.mail |
    | family_name | user.surname |
    | given_name | user.givenname |
    | título | user.jobtitle |
    | cost_center | user.department |
    | locale | user.preferredlanguage |

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Retail Zipline.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Retail Zipline**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-retail-zipline-sso"></a>Configurar o SSO do Retail Zipline

Para configurar logon único no **Retail Zipline**, é necessário enviar a **URL de metadados de federação do aplicativo** para a [equipe de suporte do Retail Zipline](mailto:support@retailzipline.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-retail-zipline-test-user"></a>Criar um usuário de teste do Retail Zipline

Nesta seção, um usuário chamado Brenda Fernandes será criado no Retail Zipline. O Retail Zipline é compatível com provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se o usuário ainda não existir no Retail Zipline, será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Retail Zipline na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do Retail Zipline diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Retail Zipline para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco Retail Zipline no Painel de Acesso, se estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se estiver configurado no modo IdP, você será conectado automaticamente ao Retail Zipline para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Retail Zipline, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
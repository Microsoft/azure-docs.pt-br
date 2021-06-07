---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TravelPerk | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o TravelPerk.
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
ms.openlocfilehash: eefc47e1308dc5b59d8b57d7481a5250a7d2af93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99475736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-travelperk"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TravelPerk

Neste tutorial, você aprenderá a integrar o TravelPerk ao Azure AD (Azure Active Directory). Ao integrar o TravelPerk ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao TravelPerk.
* Permitir que seus usuários sejam conectados automaticamente ao TravelPerk com suas respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma conta do TravelPerk com assinatura Premium.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O TravelPerk é compatível com SSO iniciado por **SP**

* O TravelPerk é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-travelperk-from-the-gallery"></a>Como adicionar o TravelPerk por meio da galeria

Para configurar a integração do TravelPerk ao Azure AD, será necessário adicionar o TravelPerk por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **TravelPerk** na caixa de pesquisa.
1. Selecione **TravelPerk** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-travelperk"></a>Configurar e testar o SSO do Azure AD para o TravelPerk

Configure e teste o SSO do Azure AD com o TravelPerk usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, será necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TravelPerk.

Para configurar e testar o SSO do Azure AD com o TravelPerk, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do TravelPerk](#configure-travelperk-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do TravelPerk](#create-travelperk-test-user)** – ter um usuário equivalente à B.Fernandes no TravelPerk que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na página Integração de aplicativos do **TravelPerk** no portal do Azure, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<COMPANY>.travelperk.com/`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<COMPANY>.travelperk.com/accounts/saml2/metadata/<APPLICATION_ID>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<COMPANY>.travelperk.com/accounts/saml2/callback/<APPLICATION_ID>/?acs`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, a URL de Resposta e o Identificador reais. Os valores podem ser encontrados na sua conta do TravelPerk: acesse **Configurações da Empresa** > **Integrações** > **Logon Único**. Para obter assistência, visite o [Centro de ajuda do TravelPerk](https://support.travelperk.com/hc/en-us/articles/360052450271-How-can-I-setup-SSO-for-Azure-SAML-).

1. O aplicativo TravelPerk espera obter declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. No mapeamento padrão, **emailaddress** é mapeado com **user.mail**. No entanto, o aplicativo TravelPerk espera que **emailaddress** seja mapeado com **user.userprincipalname**. Para o TravelPerk, você precisa editar o mapeamento de atributos: clique no ícone **Editar** e altere o mapeamento de atributos. Para editar um atributo, basta clicar nele para abrir o modo de edição.

    ![image](common/default-attributes.png)

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o TravelPerk**, copie a(s) URL(s) apropriada(s) com base em seus requisitos.

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

Nesta seção, você permitirá que a usuária B.Fernandes use o logon único do Azure concedendo acesso ao TravelPerk.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TravelPerk**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-travelperk-sso"></a>Configurar o SSO do TravelPerk

Para configurar o logon único no lado do **TravelPerk**, será necessário enviar o **XML de Metadados Federados** baixado, além das URLs apropriadas e copiadas do portal do Azure para a [equipe de suporte do TravelPerk](mailto:trex@travelperk.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-travelperk-test-user"></a>Criar um usuário de teste do TravelPerk

Nesta seção, uma usuária chamada B.Fernandes será criada no TravelPerk. O TravelPerk é compatível com o provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Caso não tenha um perfil de usuário no TravelPerk, um usuário será criado quando você tentar acessar o TravelPerk.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do TravelPerk, na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de Logon do TravelPerk diretamente e inicie o fluxo de logon nela.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco TravelPerk no Painel de Acesso, você será redirecionado para a URL de Logon do TravelPerk. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o TravelPerk, será possível impor controles de sessão, que protegem contra a infiltração e exfiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

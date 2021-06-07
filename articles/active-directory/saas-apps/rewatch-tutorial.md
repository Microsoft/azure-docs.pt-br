---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Rewatch | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Rewatch.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: jeedes
ms.openlocfilehash: 032957b49c7ef9170a7e1aaa63eaa66a3c3e8401
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648502"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rewatch"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Rewatch

Neste tutorial, você aprenderá a integrar o Rewatch ao Azure AD. (Active Directory). Quando você integrar o Rewatch ao Azure AD, será possível:

* Controlar no Azure AD quem tem acesso ao Rewatch.
* Permitir que os seus usuários entrem automaticamente no Rewatch com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Rewatch.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Rewatch é compatível com o SSO iniciado por **SP e IDP**
* O Rewatch dá suporte ao provisionamento de usuário **Just In Time**

## <a name="adding-rewatch-from-the-gallery"></a>Como adicionar o Rewatch por meio da galeria

Para configurar a integração do Rewatch ao Azure AD, você precisa adicionar o Rewatch por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Rewatch** na caixa de pesquisa.
1. Selecione **Rewatch** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-rewatch"></a>Configurar e testar o SSO do Azure AD para o Rewatch

Configure e teste o SSO do Azure AD com o Rewatch usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Rewatch.

Para configurar e testar o SSO do Azure AD com o Rewatch, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Rewatch](#configure-rewatch-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Rewatch](#create-rewatch-test-user)** – Para ter um equivalente de B.Fernandes no Rewatch que esteja vinculado à declaração de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Rewatch**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://rewatch.tv/login`

1. O aplicativo Rewatch espera declarações SAML em um formato específico, o que requer que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Rewatch espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | --------------- | --------- |
    | Agrupar | user.groups |


1. Clique em **Save** (Salvar).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Rewatch**, copie as URLs apropriadas de acordo com os seus requisitos.

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

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure permitindo que ela tenha acesso ao Rewatch.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Rewatch**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-rewatch-sso"></a>Configurar o SSO do Rewatch

1. Em outra janela do navegador da Web, entre como um administrador no seu site da empresa do Rewatch.

1. Clique em **Console do Administrador** no menu do lado esquerdo.

    ![O console do administrador do Rewatch na home page.](./media/rewatch-tutorial/admin-console.png)

1. Acesse **Segurança** e execute as etapas a seguir na seção **logon único do SAML**.

    ![Seção de logon único do SAML.](./media/rewatch-tutorial/security.png)

    a. Na caixa de texto **URL de destino do SSO do IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Abra o **Certificado (Base64)** baixado no portal do Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **Certificado do IdP**.

    c. Marque **Habilitar logon do SAML neste canal** e clique em **Salvar**.

### <a name="create-rewatch-test-user"></a>Criar um usuário de teste do Rewatch

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Rewatch. O Rewatch é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Rewatch, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Rewatch, na qual será possível iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Rewatch e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Rewatch para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Rewatch em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Rewatch para o qual o SSO foi configurado. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Rewatch, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
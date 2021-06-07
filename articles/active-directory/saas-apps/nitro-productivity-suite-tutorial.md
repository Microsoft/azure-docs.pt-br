---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Nitro Productivity Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Nitro Productivity Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 3280c97e735e68aa36f018a8de59964ade9567b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181886"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Nitro Productivity Suite

Neste tutorial, você aprenderá a integrar o Nitro Productivity Suite ao Microsoft Azure AD (Azure Active Directory). Ao integrar o Nitro Productivity Suite ao Azure AD, você pode:

* Controlar no Azure AD que tem acesso ao Nitro Productivity Suite.
* Permitir que os usuários sejam conectados automaticamente ao Nitro Productivity Suite com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma [assinatura Enterprise](https://www.gonitro.com/pricing) do Nitro Productivity Suite.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Nitro Productivity Suite é compatível com o SSO iniciado por **SP** e **IDP**.
* O Nitro Productivity Suite é compatível com o provisionamento de usuário **Just-In-Time**.

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Adicionar o Nitro Productivity Suite da galeria

Para configurar a integração do Nitro Productivity Suite com o Azure AD, é necessário adicionar o Nitro Productivity Suite da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Nitro Productivity Suite** na caixa de pesquisa.
1. Selecione **Nitro Productivity Suite** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Configurar e testar o logon único do Azure AD para o Nitro Productivity Suite

Configure e teste o SSO do Azure AD com o Nitro Productivity Suite usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Nitro Productivity Suite.

Para configurar e testar o SSO do Azure AD com o Nitro Productivity Suite, conclua os seguintes blocos de construção:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.

    a. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
    
    b. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
    
2. [Crie um usuário de teste do Nitro Productivity Suite](#create-a-nitro-productivity-suite-test-user) para ter um equivalente de B.Fernandes no Nitro Productivity Suite que esteja vinculado à representação do usuário no Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Nitro Productivity Suite**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na seção **Certificado de autenticação SAML**, localize **Certificados (Base64)** . Selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![Captura de tela da seção Certificado de Autenticação SAML, com o link Download realçado](common/certificatebase64.png)
    
1. Na seção **Configurar o Nitro Productivity Suite**, selecione o ícone de cópia ao lado da **URL de Logon**.
    
    ![Captura de tela da seção Configurar o Nitro Productivity Suite, com URLs e ícones de cópia realçados](common/copy-configuration-urls.png)
    
1. No [Portal de administração do Nitro](https://admin.gonitro.com/), na página **Configurações do Enterprise**, localize a seção **Logon Único**. Selecione **Configuração de SSO do SAML**.

    a. Cole a **URL de Logon** da etapa anterior no campo **URL de Entrada**.
    
    b. Carregue o **Certificado (Base64)** da etapa anterior no campo **Certificado de Autenticação X509**.
    
    c. Selecione **Enviar**.
    
    d. Selecione **Habilitar Logon Único**.


1. Volte para o [portal do Azure](https://portal.azure.com/). Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o Logon Único com SAML, com o ícone de lápis realçado](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IdP**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, copie e cole o campo **ID da Entidade SAML** do [Portal de Administração do Nitro](https://admin.gonitro.com/). Ele deve ter o seguinte padrão: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Na caixa de texto **URL de Resposta**, copie e cole o campo **URL do ACS** do [Portal de Administração do Nitro](https://admin.gonitro.com/). Ele deve ter o seguinte padrão: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Selecione **Definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado por **SP**:

    Na caixa de texto **URL de Logon**, digite a URL: `https://sso.gonitro.com/login`

1. Clique em **Salvar**.

1. O aplicativo do Nitro Productivity Suite espera que as declarações SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela de atributos padrão](common/default-attributes.png)

1. Além dos atributos anteriores, o aplicativo do Nitro Productivity Suite espera que mais alguns atributos sejam passados novamente na resposta SAML. Esses atributos são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome  |  Atributo de origem|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de seleção **Mostrar senha** e, em seguida, anote a senha.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá a B.Fernandes acesso ao Nitro Productivity Suite, que permitirá o uso do logon único do Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Nitro Productivity Suite**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Criar um usuário de teste do Nitro Productivity Suite

O Nitro Productivity Suite é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há nenhuma ação para você executar. Se um usuário ainda não existir no Nitro Productivity Suite, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Nitro Productivity Suite, na qual você poderá iniciar o fluxo de logon.  

2. Acesse a URL de Logon do Nitro Productivity Suite diretamente e inicie o fluxo de logon nele.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no Nitro Productivity Suite para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco Nitro Productivity Suite no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Nitro Productivity Suite para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o Nitro Productivity Suite, você poderá impor controles de sessão, que protegem contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
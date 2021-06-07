---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Terraform Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Terraform Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: c26224f388c09ec758373c877004f5485bb420df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97725067"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-terraform-cloud"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Terraform Cloud

Neste tutorial, você aprenderá a integrar o Terraform Cloud ao Azure Active Directory (Azure AD). Com a integração do Terraform Cloud ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao Terraform Cloud.
* Permitir que os usuários sejam conectados automaticamente ao Terraform Cloud com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Terraform Cloud habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Terraform Cloud é compatível com SSO iniciado por **SP e IDP**
* O Terraform Cloud é compatível com o provisionamento de usuário **Just-In-Time**


## <a name="adding-terraform-cloud-from-the-gallery"></a>Como adicionar o Terraform Cloud por meio da galeria

Para configurar a integração do Terraform Cloud com o Azure AD, você precisa adicionar o Terraform Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Terraform Cloud** na caixa de pesquisa.
1. Selecione **Terraform Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-terraform-cloud"></a>Configurar e testar o SSO do Azure AD para o Terraform Cloud

Configure e teste o SSO do Azure AD com o Terraform Cloud usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Terraform Cloud.

Para configurar e testar o SSO do Azure AD com o Terraform Cloud, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Terraform Cloud](#configure-terraform-cloud-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Terraform Cloud](#create-terraform-cloud-test-user)** – para ter um equivalente de B.Fernandes no Terraform Cloud que esteja vinculado à representação da usuária no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Terraform Cloud**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://app.terraform.io/sso/saml/samlconf-<ID>/metadata`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.terraform.io/session`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Entre em contato com [equipe de suporte do cliente do Terraform Cloud](mailto:tf-cloud@hashicorp.support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Terraform Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Terraform Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-terraform-cloud-sso"></a>Configure SSO do Terraform Cloud

1. Para automatizar a configuração no Terraform Cloud, você precisará instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Terraform Cloud** para ser direcionado ao aplicativo Terraform Cloud. Nele, forneça as credenciais de administrador para entrar no Terraform Cloud. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Terraform Cloud manualmente, em outra janela do navegador da Web, entre em seu site da empresa do Terraform Cloud como administrador.

2. Acesse **Configurações > SSO > Editar Configurações**

    ![Configurações do Terraform Cloud](./media/terraform-cloud-tutorial/sso-settings.png)

3. Na página **Editar SSO**, execute as etapas a seguir.

    ![Editar SSO do Terraform Cloud](./media/terraform-cloud-tutorial/edit-sso.png)

    a. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    b. Na caixa de texto **Identificador ou ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Abra o **Certificado** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado Público**.

    d. Clique em **Salvar configurações**.

### <a name="create-terraform-cloud-test-user"></a>Criar um usuário de teste do Terraform Cloud

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Terraform Cloud. O Terraform Cloud é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se nenhum usuário existir no Terraform Cloud, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Terraform Cloud, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Terraform Cloud, na qual você iniciará o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Terraform Cloud para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco Terraform Cloud no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Terraform Cloud para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Terraform Cloud, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
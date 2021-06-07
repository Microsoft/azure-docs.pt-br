---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PerimeterX | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PerimeterX.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/27/2020
ms.author: jeedes
ms.openlocfilehash: 29ffaaa1b1b6efbcd5523a76018c92645e13d187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181780"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeterx"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PerimeterX

Neste tutorial, você aprenderá a integrar o PerimeterX ao Azure AD (Azure Active Directory). Com a integração do PerimeterX ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao PerimeterX.
* Permitir que os usuários sejam conectados automaticamente ao PerimeterX com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do PerimeterX habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.


* O PerimeterX é compatível com SSO iniciado por **IDP**

## <a name="adding-perimeterx-from-the-gallery"></a>Como adicionar o PerimeterX por meio da galeria

Para configurar a integração do PerimeterX com o Azure AD, você precisa adicionar o PerimeterX da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **PerimeterX** na caixa de pesquisa.
1. Selecione **PerimeterX** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-perimeterx"></a>Configurar e testar o SSO do Azure AD para o PerimeterX

Configure e teste o SSO do Azure AD com o PerimeterX usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PerimeterX.

Para configurar e testar o SSO do Azure AD com o PerimeterX, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do PerimeterX](#configure-perimeterx-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste da PerimeterX](#create-perimeterx-test-user)** para ter um equivalente de B.Fernandes no PerimeterX que esteja vinculado à representação da usuária no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **PerimeterX**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.


1. Seu aplicativo PerimeterX espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo PerimeterX espera que mais alguns atributos (mostrados abaixo) sejam passados novamente na resposta SAML. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome | Atributo de Origem|
    | ------------ | --------- |
    | firstName | user.givenname |
    | lastName  | user.surname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o PerimeterX**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao PerimeterX.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **PerimeterX**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-perimeterx-sso"></a>Configurar o SSO do PerimeterX

1. Faça logon no console do PerimeterX com permissões de administrador.

1. Navegue até **Administrador > CONTAS** 

    ![SSO do PerimeterX](./media/perimeterx-tutorial/accounts.png)

1. Clique em **Editar**

4.  No diálogo Editar Conta, realize as etapas a seguir.

    ![Conta de edição de SSO do PerimeterX](./media/perimeterx-tutorial/saml-sso.png)

    a.  Marque **Habilitar SSO (logon único)**

    b.  Selecione **SAML do Azure**.

    c.  Na caixa de texto **Ponto de extremidade SAML**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    d. Na caixa de texto **Emissor**, cole o valor do Identificador do Azure AD copiado do portal do Azure.

    e. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado X.509**.

    f. Clique em **Salvar Alterações**

### <a name="create-perimeterx-test-user"></a>Criar um usuário de teste do PerimeterX

Consulte o [Guia Gerenciar Usuários do PerimeterX](https://docs.perimeterx.com/pxconsole/docs/managing-users) para obter instruções sobre como criar o usuário de teste do PerimeterX.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

1. Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao PerimeterX para o qual configurou o SSO

1. Use o Painel de Acesso da Microsoft. Quando você clicar no bloco do PerimeterX no Painel de Acesso, será conectado automaticamente ao PerimeterX para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o PerimeterX, você poderá impor um controle de sessão, que fornece proteção contra o vazamento e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
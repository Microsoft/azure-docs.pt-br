---
title: 'Tutorial: Integração do Azure Active Directory ao 123FormBuilder SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o 123FormBuilder SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.openlocfilehash: aa4bab2f7ecb90c61e22de46b01a5ed81342a408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319174"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao 123FormBuilder SSO

Neste tutorial, você aprenderá a integrar o 123FormBuilder SSO ao Azure AD (Azure Active Directory). Ao integrar o 123FormBuilder SSO ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao 123FormBuilder SSO.
* Permitir que seus usuários sejam conectados automaticamente ao 123FormBuilder SSO com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do 123FormBuilder SSO habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O 123FormBuilder SSO é compatível com o SSO iniciado por **SP e IdP**.
* O 123FormBuilder SSO é compatível com o provisionamento de usuário **just-in-time**.
* Depois de configurar o 123FormBuilder SSO, você poderá impor um controle de sessão, que fornece proteção contra o vazamento e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>Adicionar o 123FormBuilder SSO da galeria

Para configurar a integração do 123FormBuilder SSO ao Azure AD, é necessário adicionar o 123FormBuilder SSO da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **123FormBuilder SSO** na caixa de pesquisa.
1. Selecione **123FormBuilder SSO** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>Configurar e testar o logon único do Azure AD para o 123FormBuilder SSO

Configure e teste o SSO do Azure AD com o 123FormBuilder SSO usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no 123FormBuilder SSO.

Para configurar e testar o SSO do Azure AD com o 123FormBuilder SSO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do 123FormBuilder](#configure-123formbuilder-sso)** : para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do 123FormBuilder SSO](#create-123formbuilder-sso-test-user)** para ter um equivalente de B.Fernandes no 123FormBuilder SSO que seja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **123FormBuilder SSO**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata`


    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Esses valores não são reais. Você precisará atualizar esse valor com o Identificador e as URLs reais que são explicadas posteriormente no tutorial.

1. Na página **Configurar o logon único com SAML**, na seção **Certificado de Autenticação SAML**, localize o **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o 123FormBuilder SSO**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao 123FormBuilder SSO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **123FormBuilder SSO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-123formbuilder-sso"></a>Configurar o 123FormBuilder SSO

1. Para configurar o logon único no lado do **123FormBuilder SSO**, acesse [https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) e siga estas etapas:

    ![Captura de tela que mostra a tela de configuração do SAML de SSO – Provedor de Identidade.](./media/123formbuilder-tutorial/submit.png) 

    a. Na caixa de texto **Email**, digite o email do usuário como `B.Simon@Contoso.com`.

    b. Clique em **Carregar** e procure o arquivo XML de Metadados baixado no portal do Azure.

    c. Clique em **ENVIAR FORMULÁRIO**.

2. Em **Microsoft Azure AD – Logon único – Definir Configurações do Aplicativo**, realize as seguintes etapas:

    ![Configurar o logon único](./media/123formbuilder-tutorial/url3.png)

    a. Se desejar configurar o aplicativo no **modo iniciado pelo IDP**, copie o valor de **IDENTIFICADOR** da instância e cole-o na caixa de texto **Identificador** da seção **Configuração Básica do SAML** no portal do Azure.

    b. Se desejar configurar o aplicativo no **modo iniciado pelo IDP**, copie o valor da **URL DE RESPOSTA** da instância e cole-o na caixa de texto **URL de Resposta** da seção **Configuração Básica do SAML** no portal do Azure.

    c. Se desejar configurar o aplicativo no **modo iniciado pelo SP**, copie o valor da **URL DE LOGON** da instância e cole-o na caixa de texto **URL de Logon** da seção **Configuração Básica do SAML** no portal do Azure.

### <a name="create-123formbuilder-sso-test-user"></a>Criar um usuário de teste do 123FormBuilder SSO

Nesta seção, uma usuária chamada Brenda Fernandes será criada no 123FormBuilder SSO. O 123FormBuilder SSO é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no 123FormBuilder SSO, um usuário será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do 123FormBuilder SSO no Painel de Acesso, você deverá ser conectado automaticamente ao 123FormBuilder SSO para o qual você configurou um SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o 123FormBuilder SSO com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
---
title: 'Tutorial: Integração do Azure Active Directory ao OpsGenie | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o OpsGenie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 2ab1f9ee6095dfc0f708ec33622aad6f70fcae65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728501"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao OpsGenie

Neste tutorial, você aprenderá a integrar o OpsGenie ao Azure AD (Azure Active Directory). Ao integrar o OpsGenie ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao OpsGenie.
* Permitir que os usuários sejam conectados automaticamente ao OpsGenie com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do OpsGenie.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O OpsGenie dá suporte ao SSO iniciado por **IdP**

## <a name="adding-opsgenie-from-the-gallery"></a>Adição do OpsGenie da galeria

Para configurar a integração do OpsGenie ao Azure AD, você precisa adicionar o OpsGenie a partir da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **OpsGenie** na caixa de pesquisa.
1. Escolha **OpsGenie** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>Configurar e testar o SSO do Azure AD para o OpsGenie

Configure e teste o SSO do Azure AD com o OpsGenie usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do OpsGenie.

Para configurar e testar o SSO do Azure AD com o OpsGenie, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do OpsGenie](#configure-opsgenie-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do OpsGenie](#create-opsgenie-test-user)** : para ter um equivalente de B.Fernandes no OpsGenie que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **OpsGenie**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e o Identificador reais, que serão explicados mais adiante neste tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

1. Na seção **Configurar o OpsGenie**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao OpsGenie.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **OpsGenie**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-opsgenie-sso"></a>Configurar o SSO do OpsGenie

1. Para automatizar a configuração no OpsGenie, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o OpsGenie** e você será direcionado ao aplicativo OpsGenie. Nele, forneça as credenciais de administrador para entrar no OpsGenie. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o OpsGenie manualmente, em uma janela diferente do navegador da Web, entre no site da empresa do OpsGenie como administrador.

2. Clique em **Configurações** e na guia **Logon único**.
   
    ![Logon único do OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Para habilitar o SSO, selecione **Habilitado**.
   
    ![Uma captura de tela que mostra a caixa de seleção "Habilitado" selecionada.](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. Na seção **Provedor**, clique na guia **Azure Active Directory**.
   
    ![Uma captura de tela que mostra a seção "Provedor" com a guia "Azure Active Directory" selecionada.](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Na página de diálogo Azure Active Directory, execute as seguintes etapas:
   
    ![Uma captura de tela que mostra a seção "Logon único" com a alternância "Habilitar logon único", além de "Ponto de extremidade do SAML 2.0" e "URL de metadados".](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Copie o valor do **URI da ID do Aplicativo** e cole-o na caixa de texto **Identificador (ID da Entidade)** da seção **Configuração Básica do SAML** no portal do Azure.

    a. Copie o valor da **URL de Reposta** e cole-o na caixa de texto **URL de Resposta** da seção **Configuração Básica do SAML** no portal do Azure.

    a. Na caixa de texto **Ponto de Extremidade do SAML 2.0**, cole o valor da **URL de Logon** copiado do portal do Azure.
    
    b. Na caixa de texto **URL de Metadados:** , cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure.
    
    c. Para habilitar o SSO, ative a alternância **Habilitar logon único**.

    d. Clique em **Aplicar configurações de SSO**.

### <a name="create-opsgenie-test-user"></a>Criar um usuário de teste do OpsGenie

O objetivo desta seção é criar um usuário chamado B.Fernandes no OpsGenie. 

1. Em uma janela de navegador da Web, entre no seu locatário do OpsGenie como administrador.

2. Navegue até a lista Usuários clicando em **Usuários** no painel esquerdo.
   
    ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Clique em **Adicionar Usuário**.

4. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
   
    ![Uma captura de tela que mostra a caixa de diálogo "Adicionar Usuário" com as caixas de texto "Email" e "Nome completo" realçadas, bem como o botão "Salvar" selecionado.](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Na caixa de texto **Email**, digite o endereço de email de B.Fernandes endereçado no Azure Active Directory.
   
    b. Na caixa de texto **Nome Completo**, digite **B.Fernandes**.
   
    c. Clique em **Save** (Salvar). 

> [!NOTE]
> B.Fernandes receberá um email com instruções para configurar o perfil dela.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você deverá ser conectado automaticamente ao OpsGenie para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do OpsGenie nos Meus Aplicativos, você deverá ser conectado automaticamente ao OpsGenie para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

* Depois de configurar o OpsGenie, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
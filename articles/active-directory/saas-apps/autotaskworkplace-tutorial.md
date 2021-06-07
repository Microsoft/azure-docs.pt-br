---
title: 'Tutorial: Integração do Azure Active Directory ao Autotask Workplace | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Autotask Workplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 1a2602ee3b8fad6e87a778ab966f7cb1f8aad8c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649930"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Tutorial: Integração do Azure Active Directory ao Autotask Workplace

Neste tutorial, você aprenderá a integrar o Autotask Workplace ao Azure AD (Azure Active Directory). Ao integrar o Autotask Workplace ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Autotask Workplace.
* Permitir que os seus usuários entrem automaticamente no Autotask Workplace com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Autotask Workplace habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Autotask Workplace é compatível com SSO iniciado por **SP e IDP**

## <a name="add-autotask-workplace-from-the-gallery"></a>Adicionar o Autotask Workplace por meio da galeria

Para configurar a integração do Autotask Workplace ao Azure AD, você precisa adicionar o Autotask Workplace à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Autotask Workplace** na caixa de pesquisa.
1. Selecione **Autotask Workplace** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Configurar e testar o SSO do Azure AD para Autotask Workplace

Configure e teste o SSO do Azure AD com o Autotask Workplace usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Autotask Workplace.

Para configurar e testar o SSO do Azure AD com o Autotask Workplace, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Autotask Workplace](#configure-autotask-workplace-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Autotask Workplace](#create-autotask-workplace-test-user)** – Para ter um equivalente de B.Fernandes no Autotask Workplace vinculado à declaração de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Autotask Workplace**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Captura de tela que mostra a Configuração Básica de SAML, em que você pode inserir o Identificador e a URL de Resposta e selecionar Salvar.](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Captura de tela que mostra Definir URLs adicionais, em que você pode inserir uma URL de Logon.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o Autotask Workplace**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure permitindo que ela tenha acesso ao Autotask Workplace.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Autotask Workplace**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-autotask-workplace-sso"></a>Configurar o SSO do Autotask Workplace

1. Em uma janela de navegador da Web diferente, faça logon no Workplace Online usando as credenciais de administrador.

    > [!Note]
    > Ao configurar o IdP, um subdomínio precisará ser especificado. Para confirmar o subdomínio correto, faça logon no Workplace Online. Depois de conectado, tome nota do subdomínio na URL. O subdomínio é a parte entre o "https://" e ".awp.autotask.net/" e deve ser us, eu, ca or au.

2. Vá até **Configuração** > **Logon Único** e execute as seguintes etapas:

    ![Configuração de Logon Único do Autotask](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. Selecione a opção **Arquivo de Metadados XML** e, em seguida, carregue o **XML de Metadados de Federação** baixado do portal do Azure.

    b. Clique em **HABILITAR SSO**.

    ![Configuração de aprovação de Logon Único do Autotask](./media/autotaskworkplace-tutorial/configuration-2.png)

    c. Selecione a caixa de seleção **Confirmo que essas informações estão corretas e eu confio neste IdP**.

    d. Clique em **APROVAR**.

> [!Note]
> Se você precisar de assistência para configurar o Autotask Workplace, consulte [essa página](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para obter ajuda com sua conta do Workplace.

### <a name="create-autotask-workplace-test-user"></a>Criar um usuário de teste do Autotask Workplace

Nesta seção, você criará um usuário chamado Brenda Fernandes no Autotask Workplace. Trabalhe com a [equipe de suporte do Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) para adicionar os usuários à plataforma Autotask Workplace.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Autotask Workplace, na qual será possível iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Autotask Workplace e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Autotask Workplace para o qual o SSO foi configurado. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Autotask Workplace em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Autotask Workplace para o qual o SSO foi configurado. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Autotask Workplace, você poderá impor um controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
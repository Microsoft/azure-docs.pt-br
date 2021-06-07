---
title: 'Tutorial: Integração do Azure Active Directory com o ArcGIS Enterprise | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9cde75440292fffb830656c32181d7be64a55f3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645484"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Tutorial: Integração do Azure Active Directory com o ArcGIS Enterprise

Neste tutorial, você aprenderá a integrar o ArcGIS Enterprise ao Azure AD (Azure Active Directory). Ao integrar o ArcGIS Enterprise ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao ArcGIS Enterprise.
* Permitir que os usuários entrem automaticamente no ArcGIS Enterprise com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do ArcGIS Enterprise habilitada para SSO (logon único).

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ArcGIS Enterprise dá suporte ao SSO iniciado por **SP e IdP**.
* O ArcGIS Enterprise dá suporte ao provisionamento de usuário **Just-In-Time**.

## <a name="add-arcgis-enterprise-from-the-gallery"></a>Adicionar o ArcGIS Enterprise por meio da galeria

Para configurar a integração do ArcGIS Enterprise com o Azure AD, você precisa adicionar o ArcGIS Enterprise da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ArcGIS Enterprise** na caixa de pesquisa.
1. Selecione **ArcGIS Enterprise** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Configurar e testar o SSO do Azure AD para o ArcGIS Enterprise

Configure e teste o SSO do Azure AD com o ArcGIS Enterprise usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ArcGIS Enterprise.

Para configurar e testar o SSO do Azure AD com o ArcGIS Enterprise, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ArcGIS Enterprise](#configure-arcgis-enterprise-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** : para ter um equivalente de B.Fernandes no ArcGIS Enterprise que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **ArcGIS Enterprise**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo iniciado por **IDP**:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `<EXTERNAL_DNS_NAME>.portal`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do ArcGIS Enterprise](mailto:support@esri.com) para obter esses valores. Você obterá o valor do identificador na seção **Definir provedor de identidade**, que será explicada mais adiante neste tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao ArcGIS Enterprise.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ArcGIS Enterprise**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-arcgis-enterprise-sso"></a>Configurar o SSO do ArcGIS Enterprise

1. Para automatizar a configuração no ArcGIS Enterprise, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o ArcGIS Enterprise** para ser direcionado ao aplicativo ArcGIS Enterprise. Nele, forneça as credenciais de administrador para entrar no ArcGIS Enterprise. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o ArcGIS Enterprise manualmente, faça logon no seu site de empresa do ArcGIS Enterprise como administrador.


1. Selecione **Organização > EDITAR CONFIGURAÇÕES**.

    ![Captura de tela que mostra a guia Organização do ArcGIS Enterprise com Editar configurações em destaque.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Selecione a guia **Segurança**.

    ![Captura de tela que mostra a guia Segurança selecionada.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Role para baixo até a seção **Logons corporativos via SAML** e selecione **DEFINIR LOGON CORPORATIVO**.

    ![Captura de tela que mostra Logons Corporativos via SAML, em que pode selecionar Definir Logon Corporativo.](./media/arcgisenterprise-tutorial/configure-3.png)

1. Na seção **Configurar Provedores de Identidade**, execute as seguintes etapas:

    ![Captura de tela que mostra Definir Provedor de Identidade, em que pode executar as etapas descritas aqui.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Forneça um nome como **Teste do Azure Active Directory** na caixa de texto **Nome**.

    b. Na caixa de texto **URL**, cole o valor da **URL de Metadados de Federação do Aplicativo** que você copiou do portal do Azure.

    c. Clique em **Mostrar configurações avançadas**, copie o valor da **ID da Entidade** e cole-o na caixa de texto **Identificador** na seção **Domínio e URLs do ArcGIS Enterprise** no portal do Azure.

    ![Captura de tela que mostra onde obter a ID da Entidade e atualizar o provedor de identidade.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Clique em **ATUALIZAR PROVEDOR DE IDENTIDADE**.

### <a name="create-arcgis-enterprise-test-user"></a>Criar um usuário de teste do ArcGIS Enterprise

Nesta seção, um usuário chamado Brenda Fernandes é criado no ArcGIS Enterprise. O ArcGIS Enterprise dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no ArcGIS Enterprise, um novo usuário será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do ArcGIS Enterprise](mailto:support@esri.com).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do ArcGIS Enterprise, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do ArcGIS Enterprise e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no ArcGIS Enterprise, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do ArcGIS Enterprise em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no ArcGIS Enterprise, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o ArcGIS Enterprise, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
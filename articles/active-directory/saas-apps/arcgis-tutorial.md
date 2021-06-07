---
title: 'Tutorial: Integração do Azure Active Directory ao ArcGIS Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 7a445eefa31e741562105e89fa105d404ccc0c7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646343"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integração do Azure Active Directory ao ArcGIS Online

Neste tutorial, você aprenderá a integrar o ArcGIS Online ao Azure AD (Azure Active Directory). Ao integrar o ArcGIS Online ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao ArcGIS Online.
* Permitir que os usuários sejam conectados automaticamente ao ArcGIS Online com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do ArcGIS Online.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ArcGIS Online dá suporte ao SSO iniciado por **SP**.

## <a name="add-arcgis-online-from-the-gallery"></a>Adicionar o ArcGIS Online por meio da galeria

Para configurar a integração do ArcGIS Online ao Azure AD, você precisa adicionar o ArcGIS Online à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ArcGIS Online** na caixa de pesquisa.
1. Selecione **ArcGIS Online** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-online"></a>Configurar e testar o SSO do Azure AD para o ArcGIS Online

Configure e teste o SSO do Azure AD com o ArcGIS Online usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ArcGIS Online.

Para configurar e testar o SSO do Azure AD com o ArcGIS Online, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ArcGIS Online](#configure-arcgis-online-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do ArcGIS Online](#create-arcgis-online-test-user)** – Para ter um equivalente de B.Fernandes no ArcGIS Online que esteja vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **ArcGIS Online**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.maps.arcgis.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do ArcGIS Online](https://support.esri.com/en/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Para automatizar a configuração no **ArcGIS Online**, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![image](./media/arcgis-tutorial/install-extension.png)

7. Depois de adicionar a extensão ao navegador, clique em **configurar ArcGIS Online**. Você será direcionado para o aplicativo ArcGIS Online. Lá, forneça as credenciais de administrador para entrar no ArcGIS Online. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas na seção **Configurar o Logon Único do ArcGIS Online**.

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

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure permitindo que ela tenha acesso ao ArcGIS Online.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ArcGIS Online**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-arcgis-online-sso"></a>Configurar o SSO do ArcGIS Online

1. Se quiser configurar o ArcGIS Online manualmente, abra uma nova janela do navegador da Web, entre no site corporativo do ArcGIS Online como administrador e execute as seguintes etapas:

2. Clique em **EDITAR CONFIGURAÇÕES**.

    ![Editar Configurações](./media/arcgis-tutorial/settings.png "Editar Configurações")

3. Clique em **Segurança**.

    ![Segurança](./media/arcgis-tutorial/secure.png "Segurança")

4. Em **Logons Corporativos**, clique em **DEFINIR PROVEDOR DE IDENTIDADE**.

    ![Logons Corporativos](./media/arcgis-tutorial/enterprise.png "Logons Corporativos")

5. Na página de configuração **Definir Provedor de Identidade** , realize as seguintes etapas:

    ![Definir Provedor de Identidade](./media/arcgis-tutorial/identity-provider.png "Definir Provedor de Identidade")

    a. Na caixa de texto **Nome**, digite o nome de sua organização.

    b. Para **Os metadados do Provedor de Identidade Corporativo serão fornecidos usando**, selecione **Um Arquivo**.

    c. Para carregar seu arquivo de metadados baixado, clique em **Escolher arquivo**.

    d. Clique em **DEFINIR PROVEDOR DE IDENTIDADE**.

### <a name="create-arcgis-online-test-user"></a>Criar um usuário de teste do ArcGIS Online

Para permitir que os usuários do Azure AD façam logon no ArcGIS Online, eles devem ser provisionados no ArcGIS Online.  
No caso do ArcGIS Online, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **ArcGIS** .

2. Clique em **CONVIDAR MEMBROS**.

    ![Convidar Membros](./media/arcgis-tutorial/invite.png "Convidar Membros")

3. Selecione **Adicionar membros automaticamente sem enviar um email** e, depois, clique em **AVANÇAR**.

    ![Adicionar Membros Automaticamente](./media/arcgis-tutorial/members.png "Adicionar Membros Automaticamente")

4. Na página do diálogo **Membros** , realize as seguintes etapas:

    ![Adicionar e examinar](./media/arcgis-tutorial/review.png "Adicionar e Examinar")

     a. Insira o **Email**, o **Nome** e o **Sobrenome** de uma conta válida do Azure AD que você deseja provisionar.

     b. Clique em **ADICIONAR E EXAMINAR**.
5. Examine os dados inseridos e, depois, clique em **ADICIONAR MEMBROS**.

    ![Adicionar membro](./media/arcgis-tutorial/add.png "Adicionar membro")

    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do ArcGIS Online, na qual será possível iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do ArcGIS Online e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do ArcGIS Online em Meus Aplicativos, será redirecionado à URL de logon do ArcGIS Online. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o ArcGIS Online, você poderá impor um controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
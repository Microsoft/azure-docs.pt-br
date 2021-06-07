---
title: 'Tutorial: Integração do Azure Active Directory com o RingCentral | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RingCentral.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 3c8126c051457d740d1a5414a130b4ee3fffcd8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954115"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Tutorial: Integrar o RingCentral ao Azure Active Directory

Neste tutorial, você aprenderá como integrar o RingCentral ao Azure AD (Azure Active Directory). Ao integrar o RingCentral ao Azure AD, é possível:

* Controlar quem tem acesso ao RingCentral no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao RingCentral com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do RingCentral.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O RingCentral é compatível com SSO iniciado por **IDP**.

## <a name="add-ringcentral-from-the-gallery"></a>Adicionar o RingCentral da galeria

Para configurar a integração do RingCentral ao Azure AD, você precisará adicionar o RingCentral da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **RingCentral** na caixa de pesquisa.
1. Selecione **RingCentral** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>Configurar e testar o SSO do Azure AD para o RingCentral

Configure e teste o SSO do Azure AD com o RingCentral usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do RingCentral.

Para configurar e testar o SSO do Azure AD com o RingCentral, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do RingCentral](#configure-ringcentral-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar usuário de teste do RingCentral](#create-ringcentral-test-user)** – para que haja um equivalente de B.Fernandes no RingCentral vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **RingCentral**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    1. Clique em **Carregar arquivo de metadados**.
    1. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.
    1. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção **Configuração Básica do SAML**.

    > [!Note]
    > O **arquivo de metadados do provedor de serviços** será inserido na página de configuração de SSO do RingCentral, que é explicada posteriormente no tutorial.

1. Se você não tiver o **arquivo de metadados do Provedor de Serviços**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma das URLs:
  
    | Identificador |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Na caixa de texto **URL de resposta**, digite uma das URLs:

    | URL de resposta |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao RingCentral.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **RingCentral**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ringcentral-sso"></a>Configurar o SSO do RingCentral

1. Para automatizar a configuração com o RingCentral, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, um clique em **Configurar o RingCentral** direcionará você ao aplicativo RingCentral. Nele, forneça as credenciais de administrador para entrar no RingCentral. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

1. Se desejar configurar o RingCentral manualmente, abra uma nova janela do navegador da Web, entre no site da empresa RingCentral como administrador e execute as seguintes etapas:

1. Na parte superior, clique em **Ferramentas**.

    ![Captura de tela que mostra a opção Ferramentas selecionada no site de empresa do RingCentral.](./media/ringcentral-tutorial/ringcentral-1.png)

1. Navegue até **Logon único**.

    ![Captura de tela que mostra a opção Logon Único selecionada no menu Ferramentas.](./media/ringcentral-tutorial/ringcentral-2.png)

1. Na página **Configuração de Logon Único** em **Configurações de SSO**, da **Etapa 1**, clique em **Editar** e execute as etapas a seguir:

    ![Captura de tela que mostra a página Configuração de SSO, na qual você pode selecionar Editar.](./media/ringcentral-tutorial/ringcentral-3.png)

1. Na página **Configurar Logon Único**, execute as seguintes etapas:

    ![Captura de tela que mostra a página Configurar Logon Único, na qual você pode carregar os metadados do IdP.](./media/ringcentral-tutorial/ringcentral-4.png)

    a. Clique em **Procurar** para fazer upload do arquivo de metadados que você baixou do portal do Azure.

    b. Depois que você carregar os metadados, os valores serão populados automaticamente na seção **Informações Gerais de SSO**.

    c. Na seção **Mapeamento de Atributos**, selecione **Mapear atributo de email para** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Clique em **Save** (Salvar).

    e. Na **Etapa 2** clique em **Baixar** para baixar o **arquivo de metadados do provedor de serviço** e carregue-o na seção **Configuração Básica do SAML** para preencher automaticamente os valores de **Identificador** e **URL de resposta** no portal do Azure.

    ![Captura de tela que mostra a página Configuração de SSO, na qual você pode selecionar Baixar.](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. Na mesma página, navegue até a seção **Habilitar SSO** e execute as seguintes etapas:

    ![Captura de tela que mostra a seção Habilitar SSO, na qual você pode concluir a configuração.](./media/ringcentral-tutorial/ringcentral-5.png)

    * Selecione **Habilitar Serviço de SSO**.

    * Selecione **Permitir que os usuários entrem com credenciais de SSO ou do RingCentral**.

    * Clique em **Save** (Salvar).

### <a name="create-ringcentral-test-user"></a>Criar usuário de teste do RingCentral

Nesta seção, você criará um usuário chamado Brenda Fernandes no RingCentral. Trabalhe com a [equipe de suporte ao cliente do RingCentral](https://success.ringcentral.com/RCContactSupp) para adicionar os usuários à plataforma RingCentral. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao RingCentral para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do RingCentral em Meus Aplicativos, você deverá ser conectado automaticamente ao RingCentral, para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o RingCentral, você poderá impor o controle de sessão, que protege contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
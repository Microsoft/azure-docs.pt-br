---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Saba Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Saba Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 5ce9eb41755d7faa2ce00b38dfd971313443bfb7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572086"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Saba Cloud

Neste tutorial, você aprenderá a integrar o Saba Cloud ao Azure AD (Azure Active Directory). Ao integrar o Saba Cloud ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Saba Cloud.
* Permitir que os usuários entrem automaticamente no Saba Cloud com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Saba Cloud habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Saba Cloud dá suporte ao SSO iniciado por **SP e IdP**.
* O Saba Cloud dá suporte ao provisionamento de usuário **just-in-time**.
* Agora, o aplicativo móvel do Saba Cloud pode ser configurado com o Azure AD para habilitar o SSO. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

## <a name="adding-saba-cloud-from-the-gallery"></a>Como adicionar o Saba Cloud por meio da galeria

Para configurar a integração do Saba Cloud ao Azure AD, você precisa adicionar o Saba Cloud por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Saba Cloud** na caixa de pesquisa.
1. Selecione **Saba Cloud** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Configurar e testar o SSO do Azure AD para o Saba Cloud

Configure e teste o SSO do Azure AD com o Saba Cloud por meio de um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Saba Cloud.

Para configurar e testar o SSO do Azure AD com o Saba Cloud, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Saba Cloud](#configure-saba-cloud-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Saba Cloud](#create-saba-cloud-test-user)** : para ter um equivalente de B.Fernandes no Saba Cloud que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.
1. **[Testar o SSO para o Saba Cloud (dispositivo móvel)](#test-sso-for-saba-cloud-mobile)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Saba Cloud**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `<CUSTOMER_NAME>_SPLN_PRINCIPLE`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SIGN-ON URL>/Saba/saml/SSO/alias/<ENTITY_ID>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<CUSTOMER_NAME>.sabacloud.com`

    b. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` ou caso o SAML esteja configurado para um microsite, digite uma URL usando o seguinte padrão: `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > Para obter mais informações sobre como configurar o RelayState, confira [este](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html) link.

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta, a URL de Logon e o Estado de retransmissão reais. Entre em contato com a [equipe de suporte ao cliente do Saba Cloud](mailto:support@saba.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Saba Cloud**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Saba Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Saba Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-saba-cloud-sso"></a>Configurar o SSO do Saba Cloud

1. Entre no seu site de empresa do Saba Cloud como administrador.
1. Clique no ícone do **Menu**, clique em **Administrador** e selecione a guia **Administrador do Sistema**.

    ![captura de tela do administrador do sistema](./media/saba-cloud-tutorial/system.png)

1. Em **Configurar Sistema**, selecione **Instalação do SSO do SAML** e clique no botão **CONFIGURAR SSO DO SAML**. 

    ![captura de tela da configuração](./media/saba-cloud-tutorial/configure.png)

1. Na janela pop-up, selecione **Microsite** na lista suspensa e clique em **ADICIONAR E CONFIGURAR**.

    ![captura de tela da adição do site/do microsite](./media/saba-cloud-tutorial/microsite.png)

1. Na seção **Configurar IdP**, clique em **PROCURAR** para carregar o arquivo **XML de Metadados de Federação**, que você baixou do portal do Azure. Habilite a caixa de seleção **IdP Específico do Site** e clique em **IMPORTAR**.

    ![captura de tela da importação de certificado](./media/saba-cloud-tutorial/certificate.png) 

1. Na seção **Configurar SP**, copie o valor do **Alias da Entidade** e cole esse valor na caixa de texto **Identificador (ID da Entidade)** na seção **Configuração Básica do SAML** no portal do Azure. Clique em **GERAR**.

    ![captura de tela da seção Configurar SP](./media/saba-cloud-tutorial/generate-metadata.png) 

1. Na seção **Configurar Propriedades**, verifique os campos preenchidos e clique em **SALVAR**. 

    ![captura de tela da seção Configurar Propriedades](./media/saba-cloud-tutorial/configure-properties.png) 

### <a name="create-saba-cloud-test-user"></a>Criar um usuário de teste do Saba Cloud

Nesta seção, um usuário chamado Brenda Fernandes será criado no Saba Cloud. O Saba Cloud dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no Saba Cloud, ele será criado após a autenticação.

> [!NOTE]
> Para habilitar o provisionamento de usuário just-in-time do SAML com o Saba Cloud, veja [esta](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) documentação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Saba Cloud, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Saba Cloud e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no Saba Cloud, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Saba Cloud em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no Saba Cloud, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> Se a URL de logon não for preenchida no Azure AD, o aplicativo será tratado como modo iniciado pelo IdP e, se a URL de logon for preenchida, o Azure AD sempre redirecionará o usuário ao aplicativo do Saba Cloud para o fluxo iniciado pelo provedor de serviços.

## <a name="test-sso-for-saba-cloud-mobile"></a>Testar o SSO para o Saba Cloud (dispositivo móvel)

1. Abra o aplicativo móvel do Saba Cloud, dê o **Nome do Site** na caixa de texto e clique em **ENTER**.

    ![Captura de tela da opção Nome do Site.](./media/saba-cloud-tutorial/site-name.png)

1. Insira seu **endereço de email** e clique em **Avançar**.

    ![Captura de tela do endereço de email.](./media/saba-cloud-tutorial/email-address.png)

1. Por fim, após a entrada bem-sucedida, a página do aplicativo será exibida.

    ![Captura de tela da entrada bem-sucedida.](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Saba Cloud, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).



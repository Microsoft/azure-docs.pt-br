---
title: 'Tutorial: Integração do Azure Active Directory ao HubSpot | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 91dfdcef01a121c8282b8fad2e75f67989b75dc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733226"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração do Azure Active Directory ao HubSpot

Neste tutorial, você aprenderá a integrar o HubSpot ao Azure AD (Azure Active Directory). Ao integrar o HubSpot ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao HubSpot.
* Permitir que os usuários sejam conectados automaticamente ao HubSpot com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao HubSpot, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do HubSpot com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o HubSpot ao Azure AD.

O HubSpot é compatível com os seguintes recursos:

* **Logon único iniciado por SP**
* **Logon único iniciado por IDP**

## <a name="adding-hubspot-from-the-gallery"></a>Adição do HubSpot por meio da galeria

Para configurar a integração do HubSpot ao Azure AD, você precisará adicioná-lo da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **HubSpot** na caixa de pesquisa.
1. Selecione **HubSpot** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Configurar e testar o SSO do Azure AD para o HubSpot

Nesta seção, você configurará e testará o logon único do Azure AD com o HubSpot, com base em uma usuária de teste chamada **Brenda Fernandes**. Para que o logon único funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HubSpot.

Para configurar e testar o logon único do Azure AD com o HubSpot, é necessário concluir os seguintes passos básicos:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** | Permite que os usuários usem esse recurso. |
| **[Configurar logon único do HubSpot](#configure-hubspot-single-sign-on)** | Define as configurações de logon único no aplicativo. |
| **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** | Testa o logon único do Azure AD para um usuário chamado Brenda Fernandes. |
| **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** | Habilita Brenda Fernandes a usar o logon único do Azure AD. |
| **[Criar um usuário de teste do HubSpot](#create-a-hubspot-test-user)** | Cria um equivalente de Brenda Fernandes no HubSpot que é vinculado à representação do usuário no Azure AD. |
| **[Testar o logon único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

1. No portal do Azure, na página de integração de aplicativos do **HubSpot**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. No painel **Configuração Básica de SAML**, para configurar o *modo iniciado por IDP*, conclua as seguintes etapas:

    1. Na caixa **Identificador**, insira uma URL que tenha o seguinte padrão: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>.

    1. Na caixa **URL de Resposta**, insira uma URL que tenha o seguinte padrão: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>.

    ![Informações sobre domínio e URLs de logon único do HubSpot](common/idp-intiated.png)

    > [!NOTE]
    > Para formatar as URLs, você também pode ver os padrões exibidos no painel **Configuração Básica de SAML** no portal do Azure.

1. Para configurar o aplicativo no modo *iniciado por SP*:

    1. Selecione **Definir URLs adicionais**.

    1. Na caixa **URL de logon**, digite **https:\//app.hubspot.com/login**.

    ![A opção Definir URLs adicionais](common/metadata-upload-additional-signon.png)

1. No painel **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** ao lado de **Certificado (Base64)** . Selecione uma opção de download com base em seus requisitos. Salve o certificado no computador.

    ![A opção de download do Certificado (Base64)](common/certificatebase64.png)

1. Na seção **Configurar HubSpot**, copie as seguintes URLs com base em seus requisitos:

    * URL de logon
    * Identificador do Azure AD
    * URL de logoff

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Configurar logon único do HubSpot

1. Abra uma nova guia no navegador e entre em sua conta de administrador do HubSpot.

1. Selecione o ícone **Configurações** no canto superior direito da página.

    ![O ícone Configurações no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **Padrões da Conta**.

    ![A opção Padrões da Conta no HubSpot](./media/hubspot-tutorial/config2.png)

1. Role para baixo até a seção **Segurança** e, em seguida, clique em **Configurar**.

    ![A opção Configurar no HubSpot](./media/hubspot-tutorial/config3.png)

1. Na seção **Configurar logon único**, realize as seguintes etapas:

    1. Na caixa **URL do público-alvo (ID de entidade do provedor de serviços)** caixa, selecione **Copiar** para copiar o valor. No portal do Azure, no painel **Configuração Básica de SAML**, cole o valor na caixa **Identificador**.

    1. Na caixa **URL de logon, ACS, destinatário ou o redirecionar**, selecione **Copiar** para copiar o valor. No portal do Azure, no painel **Configuração Básica de SAML**, cole o valor na caixa **URL de Resposta**.

    1. No HubSpot, na caixa **Identificador do Provedor de Identidade ou URL do Emissor**, cole o valor do **Identificador do Azure AD**, que você copiou do portal do Azure.

    1. No HubSpot, na caixa **URL de logon único do Provedor de Identidade**, cole o valor da **URL de logon** que você copiou do portal do Azure.

    1. No Bloco de Notas do Windows, abra o arquivo do Certificate(Base64) baixado. Selecione e copie o conteúdo do arquivo. Em seguida, no HubSpot, cole-o na caixa **Certificado X.509**.

    1. Selecione **Verificar**.

        ![A seção Configurar logon único no HubSpot](./media/hubspot-tutorial/config4.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao HubSpot.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **HubSpot**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-a-hubspot-test-user"></a>Criar um usuário de teste do HubSpot

Para habilitar um usuário do Azure AD a entrar no HubSpot, o usuário deve ser provisionado no HubSpot. No HubSpot, o provisionamento é uma tarefa manual.

Para provisionar uma conta de usuário no HubSpot:

1. Entre no site do HubSpot da sua empresa como administrador.

1. Selecione o ícone **Configurações** no canto superior direito da página.

    ![O ícone Configurações no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **Usuários e Equipes**.

    ![A opção Usuários e Equipes em HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecione **Criar usuário**.

    ![A opção Criar usuário no HubSpot](./media/hubspot-tutorial/user2.png)

1. Na caixa **Adicionar endereço(s) de email**, digite o endereço de email do usuário no formato brendafernandes\@contoso.com e, em seguida, selecione **Avançar**.

    ![A caixa Adicionar endereços de email, na seção Criar usuários, no HubSpot](./media/hubspot-tutorial/user3.png)

1. Na seção **Criar usuários**, selecione cada guia. Em cada guia, defina as opções e permissões relevantes para o usuário. Em seguida, selecione **Avançar**.

    ![Guias na seção Criar usuários no HubSpot](./media/hubspot-tutorial/user4.png)

1. Para enviar o convite para o usuário, selecione **Enviar**.

    ![A opção Enviar no HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > O usuário é ativado depois que o usuário aceitar o convite.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do HubSpot, na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do HubSpot diretamente e inicie dela o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no HubSpot, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do HubSpot, em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao HubSpot para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o HubSpot, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
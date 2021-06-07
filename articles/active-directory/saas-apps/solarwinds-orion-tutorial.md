---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SolarWinds Orion | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SolarWinds Orion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: jeedes
ms.openlocfilehash: 4ac5bf2756b82361388ab9f2866b80c63395f90d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591377"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SolarWinds Orion

Neste tutorial, você aprenderá a integrar o SolarWinds Orion ao Azure AD (Azure Active Directory). Ao integrar o SolarWinds Orion ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SolarWinds Orion.
* Permitir que os usuários sejam conectados automaticamente ao SolarWinds Orion com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do SolarWinds Orion habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SolarWinds Orion é compatível com SSO iniciado por **SP e IDP**.

## <a name="add-solarwinds-orion-from-the-gallery"></a>Adicionar o SolarWinds Orion por meio da galeria

Para configurar a integração do SolarWinds Orion ao Azure AD, você precisará adicionar o SolarWinds Orion por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SD SolarWinds Orion** na caixa de pesquisa.
1. Selecione **SolarWinds Orion** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Configurar e testar o SSO do Azure AD para o SolarWinds Orion

Configure e teste o SSO do Azure AD com o SolarWinds Orion com uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SolarWinds Orion.

Para configurar e testar o SSO do Azure AD com o SolarWinds Orion, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SolarWinds Orion](#configure-solarwinds-orion-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SolarWinds Orion](#create-solarwinds-orion-test-user)** – para ter um equivalente de B.Fernandes no SolarWinds Orion que esteja vinculado à representação da usuária no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SolarWinds Orion**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do SolarWinds Orion](mailto:technicalsupport@solarwinds.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Seu aplicativo SolarWinds Orion espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo SolarWinds Orion espera que mais alguns atributos sejam passados de volta na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ----------- | --------- |
    | Nome | user.givenname |
    | LastName | user.surname |
    | Email |user.mail |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o SolarWinds Orion**, copie as URLs apropriadas com base em seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Evergreen.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Evergreen**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-solarwinds-orion-sso"></a>Configurar o SSO do SolarWinds Orion

1. Faça logon no SolarWinds Orion e vá para **Configurações** -> **Todas as Configurações**.

    ![Captura de tela que mostra a opção Todas as Configurações selecionada em Configurações.](./media/solarwinds-orion-tutorial/settings.png)

1. Na seção **CONTAS DE USUÁRIO**, selecione **Configuração de SAML**.

    ![Captura de tela que mostra a opção Configuração do SAML selecionada em Contas de Usuário.](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Clique em **ADICIONAR PROVEDOR DE IDENTIDADE**.

    ![Captura de tela que mostra a opção Configuração do SAML, na qual você pode selecionar ADICIONAR PROVEDOR DE IDENTIDADE.](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Execute as seguintes etapas na página **Adicionar Provedor de Identidade**:

    ![Captura de tela que mostra a página Adicionar Provedor de Identidade do SAML, na qual você pode inserir os valores descritos.](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Vá para a guia **Configurar**.

    b. Na caixa de texto **Nome do Provedor de Identidade**, digite qualquer nome válido, como `My SSO service`.

    c. Na caixa de texto **URL de Destino do SSO**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d.  Na caixa de texto **Issuer URL**, cole o valor **Identificador do Microsoft Azure AD** que você copiou do portal do Azure.

    e. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado de Autenticação X.509**.

    f. Clique em **Save**.

### <a name="create-solarwinds-orion-test-user"></a>Criar usuário de teste do SolarWinds Orion

1. Faça logon no site do SolarWinds Orion e vá para **Configurações** -> **Todas as Configurações**.

    ![Captura de tela que mostra a opção Todas as Configurações selecionada em Configurações.](./media/solarwinds-orion-tutorial/settings.png)

1. Na seção **CONTAS DE USUÁRIO**, selecione **Gerenciar Contas**.

    ![Captura de tela que mostra a opção Configuração do SAML selecionada.](./media/solarwinds-orion-tutorial/user-accounts.png)

1. Na guia **CONTAS INDIVIDUAIS**, clique em **ADICIONAR NOVA CONTA**.

    ![Captura de tela que mostra a opção ADICIONAR NOVA CONTA selecionada em Gerenciar Contas.](./media/solarwinds-orion-tutorial/create-user.png)

1. Selecione o tipo de conta que você precisa criar, ou seja, usuários individuais ou grupos de SAML.

    ![Captura de tela que mostra a opção Adicionar Nova Conta, na qual você pode selecionar o tipo de conta.](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  Na caixa de texto **ID DO NOME**, insira o nome que deve corresponder ao nome de usuário ou do grupo, exatamente como no Azure AD.

1.  Clique em **Avançar** e envie a página.

    ![Captura de tela que mostra a opção Adicionar Nova Conta, na qual você pode inserir a ID de Nome por meio do Azure AD.](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do SolarWinds Orion, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do SolarWinds Orion e inicie o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no SolarWinds Orion para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do SolarWinds Orion, em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao SolarWinds Orion para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Após configurar o SolarWinds Orion, você pode impor o controle de sessão, que protege contra a exfiltração e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

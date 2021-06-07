---
title: 'Tutorial: integração do SSO (logon único) do Azure Active Directory ao Pulse Secure Virtual Traffic Manager | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Pulse Secure Virtual Traffic Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: 1e295075a5c1ae8daa6673757770bbef01208c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92505697"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>Tutorial: integração do SSO (logon único) do Azure Active Directory ao Pulse Secure Virtual Traffic Manager

Neste tutorial, você aprenderá a integrar o Pulse Secure Virtual Traffic Manager ao Azure AD (Azure Active Directory). Ao integrar o Pulse Secure Virtual Traffic Manager ao Azure AD, você poderá:

* Controlar no Azure AD que tem acesso ao Pulse Secure Virtual Traffic Manager.
* Permitir que os usuários, com as respectivas contas do Azure AD, sejam conectados automaticamente ao Pulse Secure Virtual Traffic Manager.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Pulse Secure Virtual Traffic Manager com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Pulse Secure Virtual Traffic Manager é compatível com o SSO iniciado por **SP**

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>Adicionar Pulse Secure Virtual Traffic Manager da galeria

Para configurar a integração do Pulse Secure Virtual Traffic Manager ao Azure AD, é necessário adicionar o Pulse Secure Virtual Traffic Manager da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Pulse Secure Virtual Traffic Manager** na caixa de pesquisa.
1. Selecione **Pulse Secure Virtual Traffic Manager** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Configurar e testar o SSO do Azure AD no Pulse Secure Virtual Traffic Manager

Configure e teste o SSO do Azure AD no Pulse Secure Virtual Traffic Manager usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente do Pulse Secure Virtual Traffic Manager.

Para configurar e testar o SSO do Azure AD com o Pulse Secure Virtual Traffic Manager, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Pulse Secure Virtual Traffic Manager](#configure-pulse-secure-virtual-traffic-manager-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste no Pulse Secure Virtual Traffic Manager](#create-pulse-secure-virtual-traffic-manager-test-user)** – Para ter um equivalente de B.Fernandes no Pulse Secure Virtual Traffic Manager, vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Pulse Secure Virtual Traffic Manager**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<published virtual server FQDN>/saml/consume`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<published virtual server FQDN>/saml/metadata`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<published virtual server FQDN>/saml/consume`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Pulse Secure Virtual Traffic Manager](mailto:support@pulsesecure.net) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Pulse Secure Virtual Traffic Manager**, copie as URLs apropriadas de acordo com os requisitos aplicáveis.

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

Nesta seção, você habilitará B.Fernandes para usar o logon único do Azure permitindo acesso ao Pulse Secure Virtual Traffic Manager.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Pulse Secure Virtual Traffic Manager**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Configurar o SSO do Pulse Secure Virtual Traffic Manager

Esta seção aborda a configuração necessária para habilitar a autenticação SAML do Azure AD no Pulse Secure Virtual Traffic Manager. Todas as alterações de configuração são feitas no Pulse Secure Virtual Traffic Manager usando a interface do usuário de administração da Web. 

#### <a name="create-a-saml-trusted-identity-provider"></a>Criar um Provedor de Identidade Confiável do SAML

a. Vá para a página **Interface do usuário de administração do dispositivo Pulse Secure Virtual Traffic Manager > Catálogo > SAML > Catálogo de Provedores de Identidade Confiável** e clique em **Editar**.

![Página de catálogos SAML](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. Adicione os detalhes do novo Provedor de Identidade Confiável do SAML, copiando as informações do aplicativo empresarial do Azure AD na página Configurações de logon único e clique em **Criar Provedor de Identidade Confiável**.

![Criar Provedor de Identidade Confiável](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* Na caixa de texto **Nome**, insira um nome para o provedor de identidade confiável. 

* Na caixa de texto **Entity_id**, insira o valor do **Identificador do Azure AD** copiado do portal do Azure.  

* Na caixa de texto **URL**, insira o valor **URL de Logon** copiado do portal do Azure. 

* Abra o **Certificado** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado**.

c. Verifique se o Provedor de Identidade do SAML foi criado com sucesso. 

![Verificar Provedor de Identidade Confiável](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>Configurar o Servidor Virtual para usar a Autenticação do Azure AD

a. Vá para a página **Interface do usuário de administração do dispositivo Pulse virtual Traffic Manager > Serviços > Servidores Virtuais** e clique em **Editar**, ao lado do servidor virtual criado anteriormente.

![Edição de servidores virtuais](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. Na seção **Autenticação**, clique em **Editar**. 

![Seção Autenticação](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. Defina as seguintes configurações de autenticação para o servidor virtual: 

1. Autenticação –

    ![Configurações de autenticação para o servidor virtual](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. Em **Auth!type**, selecione **Provedor de Serviços do SAML** 

    b. Em **Auth!verbose**, defina como "Yes" se quiser solucionar eventuais problemas de autenticação; caso contrário, deixe o valor padrão como "No" 

2. Gerenciamento de Sessão de Autenticação –

    ![Gerenciamento de Sessão de Autenticação](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. Em **Auth!session!cookie_name**, deixe o padrão como "VS_SamlSP_Auth" 

    b. Em **auth!session!timeout**, deixe o padrão como “7200” 

    c. Em **auth!session!log_external_state**, defina como "Yes" se quiser solucionar eventuais problemas de autenticação; caso contrário, deixe o valor padrão como “No” 

    d. Em **auth!session!cookie_attributes**, altere para “HTTPOnly” 

3. Provedor de Serviços do SAML –

    ![Provedor de Serviços do SAML](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a. Na caixa de texto **auth!saml!sp_entity_id**, defina com a mesma URL usada como identificador de configuração de logon único do Azure AD (ID de Entidade). Como `https://pulseweb.labb.info/saml/metadata`. 

    b. Em **auth!saml!sp_acs_url**, defina com a mesma URL usada como a URL de Reprodução da configuração de logon único do Azure AD (URL do Serviço do Consumidor de Declaração). Como `https://pulseweb.labb.info/saml/consume`. 

    c. Em **auth!saml!ipd**, selecione o **Provedor de Identidade Confiável** criado na etapa anterior. 

    d. Em auth!saml!time_tolerance, deixe o padrão como "5" segundos. 

    e. Em auth!saml!nameid_format, selecione **não especificado**.

    f. Aplique as alterações clicando em **Atualizar** na parte inferior da página.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Criar usuário de teste do Pulse Secure Virtual Traffic Manager

Nesta seção, você criará um usuário chamado Brenda Fernandes no Pulse Secure Virtual Traffic Manager. Trabalhe com a [equipe de suporte do Pulse Secure Virtual Traffic Manager](mailto:support@pulsesecure.net) para adicionar os usuários na plataforma do Pulse Secure Virtual Traffic Manager. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Pulse Secure Virtual Traffic Manager, na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de Logon do Pulse Secure Virtual Traffic Manager diretamente e inicie dela o fluxo de logon.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco do Pulse Secure Virtual Traffic Manager no Painel de Acesso, você será redirecionado para a URL de Logon do Pulse Secure Virtual Traffic Manager. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Pulse Secure Virtual Traffic Manager, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
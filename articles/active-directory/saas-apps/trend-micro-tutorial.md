---
title: 'Tutorial: Integração do SSO do Azure AD ao TMWS (Trend Micro Web Security)'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TMWS (Trend Micro Web Security).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: b76c41787d7a35fb3024fa18c0122bc966243bbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008388"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TMWS (Trend Micro Web Security)

Neste tutorial, você aprenderá a integrar a TMWS (Trend Micro Web Security) ao Azure AD (Azure Active Directory). Quando integrar o TMWS ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao TMWS.
* Permitir que os usuários sejam conectados automaticamente ao TMWS com as próprias contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do TMWS habilitada para SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O TMWS dá suporte ao SSO iniciado por SP.
* Depois de configurar o TMWS, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. Para saber como impor o controle de sessão usando Microsoft Cloud App Security, confira [Integrar e implantar o Controle de Aplicativos de Acesso Condicional para qualquer aplicativo](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-tmws-from-the-gallery"></a>Adicionar o TMWS da galeria

Para configurar a integração do TMWS ao Azure AD, você precisa adicionar o TMWS da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Active Directory**.
1. Selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, insira **TMWS (Trend Micro Web Security)** na caixa de pesquisa.
1. Selecione **TMWS (Trend Micro Web Security)** nos resultados da pesquisa e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Configurar e testar o SSO do Azure AD para o TMWS

Você vai configurar e testar o SSO do Azure AD com o TMWS usando um usuário de teste chamado B.Fernandes. Para que o SSO funcione, é necessário estabelecer um vínculo entre um usuário do Azure AD e o usuário relacionado do TMWS.

Você concluirá estas etapas básicas para configurar e testar o SSO do Azure AD com o TMWS:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) para habilitar esse recurso para seus usuários.
    1. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD.
    1. [Conceder ao usuário de teste do Azure AD](#grant-the-azure-ad-test-user-access-to-tmws) acesso ao TMWS.
    1. [Definir configurações de sincronização de usuário e de grupo no Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [Configurar o SSO do TMWS](#configure-tmws-sso) no lado do aplicativo.
1. [Testar o SSO](#test-sso) para verificar a configuração.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Conclua estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TMWS (Trend Micro Web Security)** , localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o botão de caneta da **Configuração Básica de SAML** para editar as configurações:

   ![Editar as definições de Configuração Básica do SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira valores nas seguintes caixas:

    a. Na caixa **Identificador (ID da Entidade)** , insira uma URL usando o seguinte padrão:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Na caixa **URL de Resposta**, digite esta URL:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > O valor do identificador na etapa anterior não é o valor que você deve inserir. Você precisa usar o identificador real. Você pode obter esse valor na seção **Configurações do Provedor de Serviços para o Portal de Administração do Azure** na página **Método de Autenticação** para o Azure AD em **Administração > Serviços de Diretório**.

1. O TMWS espera as asserções SAML em um formato específico, portanto, você precisa adicionar mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. Esta captura de tela mostra os atributos padrão:

    ![Atributos padrão](common/default-attributes.png)

1. Além dos atributos na captura de tela anterior, o TMWS espera que mais dois atributos sejam passados de volta na resposta SAML. Esses atributos são mostrados na tabela a seguir. Os atributos são preenchidos previamente, mas você pode alterá-los para atender às suas necessidades.
    
    | Nome | Atributo de origem|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação do SAML**, localize **Certificado (Base64)** . Selecione o link **Baixar** ao lado deste nome de certificado para baixar o certificado e salvá-lo no computador:

    ![Link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar o TMWS (Trend Micro Web Security)** , copie as URLs adequadas de acordo com seus requisitos:

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory**. Selecione **Usuários** e, em seguida, selecione **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. Na caixa **Nome**, insira `B.Simon`.  
   1. Na caixa **Nome de usuário**, insira **_username_@* companydomain *.* extension***. Por exemplo, `B.Simon@contoso.com`.
   1. Marque **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Conceder ao usuário de teste do Azure AD acesso ao TMWS

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao TMWS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TMWS (Trend Micro Web Security)** .
1. Na página de visão geral do aplicativo, na seção **Gerenciar**, selecione **Usuários e grupos**:

   ![Selecionar Usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de **Usuários** e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você espera um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Definir configurações de sincronização de usuário e de grupo no Azure AD

1. No painel esquerdo, selecione **Azure Active Directory**.

1. Em **Gerenciar**, selecione **Registros de aplicativo** e, em seguida, selecione o novo aplicativo empresarial em **Todos os aplicativos**.

1. Em **Gerenciar**, selecione **Certificados e Segredos**.

1. Na área **Segredos do cliente**, selecione **Novo segredo do cliente**.

1. Na tela **Adicionar um segredo do cliente**, adicione uma descrição (opcional), selecione um período de validade para o segredo do cliente e selecione **Adicionar**. O novo segredo do cliente aparece na área **Segredos do cliente**.

1. Registre o valor do segredo do cliente. Posteriormente, você vai inseri-lo no TMWS.

1. Em **Gerenciar**, selecione **Permissões de API**. 

1. Na janela **Permissões da API**, selecione **Adicionar uma permissão**.

1. Na guia **APIs da Microsoft** da tela **Solicitar permissões de API**, clique em **Microsoft Graph** e depois em **Permissões de aplicativo**.

1. Localize e adicione estas permissões: 

    * Group.Read.All
    * User.Read.All

1. Selecione **Adicionar Permissões**. Uma mensagem é exibida para confirmar que as configurações foram salvas. As novas permissões são exibidas na janela **Permissões de API**.

1. Em **Fornecer consentimento**, selecione **Fornecer o consentimento do administrador para *sua conta de administrador* (diretório padrão)** e selecione **Sim**. Uma mensagem é exibida para confirmar que o consentimento do administrador para as permissões solicitadas foi fornecido.

1. Selecione **Visão geral**. 

1. Registre a **ID do aplicativo (cliente)** e a **ID (locatário) do diretório** que você vê no painel direito. Posteriormente, você inserirá essas informações no TMWS. Você também pode selecionar **Nomes de domínio personalizados** em Azure **Active Directory > Gerenciar** e registrar o nome de domínio que você vê no painel direito.

## <a name="configure-tmws-sso"></a>Configurar o SSO do TMWS

Conclua estas etapas para configurar o SSO do TMWS no lado do aplicativo.

1. Entre no console de gerenciamento do TMWS e vá para **Administração** > **USUÁRIOS E AUTENTICAÇÃO** > **Serviços de Diretório**.

1. Selecione **aqui** na área superior da tela.

1. Na página **Método de Autenticação**, selecione **Azure AD**.

1. Selecione **Ligado** ou **Desligado** para configurar se os usuários do Azure AD em sua organização poderão visitar sites por meio do TMWS se os dados não estiverem sincronizados com o TMWS.

    > [!NOTE]
    > Os usuários que não estão sincronizados do Azure AD podem ser autenticados somente por meio de gateways TMWS conhecidos ou pela porta dedicada para sua organização.

1. Na seção **Configurações do Provedor de Identidade**, conclua estas etapas:

    a. Na caixa **URL do Serviço**, insira o valor da **URL de Logon** copiado do portal do Azure.

    b. Em **Atributo de nome de logon**, insira o **Nome da declaração de usuário** com o atributo de origem **user.onpremisessamaccountname** do portal do Azure.

    c. Na caixa **Certificado SSL público**, use o **Certificado (Base64)** baixado da portal do Azure.

1. Na seção **Configurações de Sincronização**, conclua estas etapas:

    a. Na caixa **Locatário**, insira **ID (locatário) do Diretório** ou o valor do **Nome de domínio personalizado** do portal do Azure.

    b. Na caixa **ID do Aplicativo**, insira o valor **ID (cliente) do Aplicativo** do portal do Azure.

    c. Na caixa **Segredo do cliente**, insira o **Segredo do cliente** no portal do Azure.

    d. Selecione **Agenda de sincronização** para sincronizar com o Azure AD manualmente ou de acordo com uma agenda. Se você selecionar **Manualmente**, sempre que houver alterações a informações do usuário do Active Directory, volte para a página **Serviços de Diretório** e execute a sincronização manual para que as informações no TMWS permaneçam atualizadas.

    e. Selecione **Testar Conexão** para verificar se o serviço do Azure AD pode ser conectado com êxito.
    
    f. Clique em **Salvar**.
 
 > [!NOTE]
 > Para obter mais informações sobre como configurar o TMWS com o Azure AD, confira [Como definir as configurações do Azure AD no TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Testar o SSO 

Depois de configurar o serviço do Azure AD e especificar o Azure AD como o método de autenticação de usuário, você pode entrar no servidor proxy do TMWS para verificar sua configuração. Depois que a conexão do Azure AD verificar sua conta, você poderá visitar a Internet.

> [!NOTE]
> O TMWS não dá suporte ao teste de logon único pelo portal do Azure AD, em **Visão geral** > **Logon único** > **Configurar o logon único com SAML** > **Teste** do seu novo aplicativo empresarial.

1. Limpe todos os cookies do navegador e depois reinicie-o. 

1. Direcione seu navegador para o servidor proxy do TMWS. Para obter mais detalhes, confira [Encaminhamento de tráfego usando arquivos PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Visite qualquer site da Internet. O TMWS direcionará você para o portal cativo do TMWS.

1. Especifique uma conta do Active Directory (formato: *domain*\\*sAMAccountName* ou *sAMAccountName*@*domain*), endereço de email ou UPN e, em seguida, selecione **Fazer logon**. O TMWS envia você para a janela de conexão do Azure AD.

1. Na janela de entrada do Azure AD, insira suas credenciais de conta do Azure AD. Agora você deve estar conectado ao TMWS.

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Trend Micro Web Security com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Trend Micro Web Security com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
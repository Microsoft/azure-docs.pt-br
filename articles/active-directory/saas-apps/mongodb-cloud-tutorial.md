---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao MongoDB Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MongoDB Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: af1c0702929e7cd700c8d19ab24e40f9c6f43a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96602148"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao MongoDB Cloud

Neste tutorial, você aprenderá a integrar o MongoDB Cloud ao Azure Active Directory (Azure AD). Ao integrar o MongoDB Cloud ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao MongoDB Cloud, MongoDB Atlas, a comunidade do MongoDB, a Universidade do MongoDB e o suporte do MongoDB.
* Permitir que os usuários sejam conectados automaticamente ao MongoDB Cloud com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma organização de nuvem do MongoDB habilitada para SSO (logon único), você pode inscrever-se para obter um [cluster gratuito](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O MongoDB Cloud é compatível com o SSO iniciado por **SP** e **IDP**.
* O MongoDB Cloud é compatível com o provisionamento de usuário **Just-In-Time**.
* Depois de configurar o MongoDB Cloud, você poderá impor o controle de sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. Para obter mais informações, confira [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mongodb-cloud-from-the-gallery"></a>Adicionar o MongoDB Cloud da galeria

Para configurar a integração do MongoDB Cloud com o Azure AD, é necessário adicionar o MongoDB Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **MongoDB Cloud** na caixa de pesquisa.
1. Selecione **MongoDB Cloud** nos resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Configurar e testar o logon único do Azure AD para o MongoDB Cloud

Configure e teste o SSO do Azure AD com o MongoDB Cloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do MongoDB Cloud.

Para configurar e testar o SSO do Azure AD com o MongoDB Cloud, conclua os seguintes blocos de construção:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
1. [Configurar o SSO do MongoDB Cloud](#configure-mongodb-cloud-sso) – para definir as configurações de logon único no lado do aplicativo.
    1. [Criar um usuário de teste do MongoDB Cloud](#create-a-mongodb-cloud-test-user) – para ter um equivalente de B.Fernandes no MongoDB Cloud que esteja vinculado à declaração de usuário do Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **MongoDB Cloud**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o Logon Único com SAML, com o ícone de lápis realçado](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IdP**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL que usa o seguinte padrão: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. Na caixa de texto **URL de Resposta**, digite uma URL que use o seguinte padrão: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Selecione **Definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado por **SP**:

    Na caixa de texto **URL de Entrada**, digite uma URL que usa o seguinte padrão: `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Para obter esses valores, entre em contato com a [equipe de suporte ao cliente do MongoDB Cloud](https://support.mongodb.com/). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo do MongoDB Cloud espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela de atributos padrão](common/default-attributes.png)

1. Além dos atributos anteriores, o aplicativo MongoDB Cloud espera que mais alguns atributos sejam passados novamente na resposta SAML. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome | Atributo de origem|
    | ---------------| --------- |
    | email | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. Na página **Configurar logon único com SAML** na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação**. Selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![Captura de tela da seção Certificado de Autenticação SAML, com o link Download realçado](common/metadataxml.png)

1. Na seção **Configurar o MongoDB Cloud**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Captura de tela da seção Configurar o MongoDB Cloud, com as URLs realçadas](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de seleção **Mostrar senha** e, em seguida, anote a senha.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que B.Fernandes use o logon único do Azure permitindo acesso ao MongoDB Cloud.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **MongoDB Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com Usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Captura de tela da página Usuários e grupos, com a função Adicionar usuário destacada](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-mongodb-cloud-sso"></a>Configurar o SSO do MongoDB Cloud

Para configurar o logon único no lado da nuvem do MongoDB, você precisa das URLs apropriadas copiadas do portal do Azure. Você também precisa configurar o aplicativo de federação para sua organização de nuvem do MongoDB. Siga as instruções na [documentação do MongoDB Cloud](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Se você tiver um problema, entre em contato com a equipe de suporte do [MongoDB Cloud](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Criar um usuário de teste do MongoDB Cloud

O MongoDB Cloud é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há nenhuma ação para você executar. Se um usuário ainda não existir no MongoDB Cloud, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do MongoDB Cloud no Painel de Acesso, você é conectado automaticamente ao MongoDB Cloud para o qual configurou o SSO. Para obter mais informações, confira [Entrar e iniciar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Inscrever-se no MongoDB Atlas no Azure](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mdb_atlas_oct2020?tab=Overview)

- [Experimente o MongoDB Cloud com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Proteger o MongoDB Cloud com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao WEDO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o WEDO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.openlocfilehash: 529c4e6433d16f9d70530ba516b5ec1426806984
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519224"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao WEDO

Neste tutorial, você aprenderá a integrar o WEDO ao Azure AD (Azure Active Directory). Ao integrar o WEDO ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao WEDO.
* Permitir que os usuários sejam conectados automaticamente ao WEDO com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do WEDO. Contate a [equipe de suporte ao cliente do WEDO](mailto:info@wedo.swiss) para obter uma assinatura de SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O WEDO dá suporte ao SSO iniciado por **SP e IdP**

* [Depois de configurar o WEDO, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wedo-from-the-gallery"></a>Como adicionar o WEDO por meio da galeria

Para configurar a integração do WEDO ao Azure AD, você precisará adicionar o WEDO por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **WEDO** na caixa de pesquisa.
1. Selecione **WEDO** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-wedo"></a>Configurar e testar o logon único do Azure AD para o WEDO

Configure e teste o SSO do Azure AD com o WEDO usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no WEDO.

Para configurar e testar o SSO do Azure AD com o WEDO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do WEDO](#configure-wedo-sso)**: para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do WEDO](#create-wedo-test-user)**: para ter um equivalente de B.Fernandes no WEDO que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **WEDO**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do WEDO](mailto:info@wedo.swiss) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo WEDO espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    | Nome | Atributo de Origem|
    | ------------ | --------- |
    | email | user.email |
    | firstName | user.firstName |
    | lastName | user.lasttName |
    | userName | user.userName |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o WEDO**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao WEDO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **WEDO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-wedo-sso"></a>Configurar o SSO do WEDO

Siga estas etapas para habilitar o SSO do Azure AD no WEDO.

1. Faça logon no [WEDO](https://login.wedo.swiss/). Você precisará ter a **função Administrador**.
1. Nas configurações de Perfil, selecione o menu **Autenticação** na seção **Configurações de rede**.
1. Na página **Autenticação SAML**, execute as seguintes etapas:

   ![Link da Autenticação SAML](media/wedo-tutorial/network-security-authentification.png)

   a. Habilite a **Autenticação SAML**.

   b. Selecione a guia **Metadados do provedor de identidade (XML)**.

   c. Abra o **XML de Metadados de Federação** baixado do portal do Azure no Bloco de notas, copie o conteúdo do XML de metadados e cole-o na caixa de texto **Certificado X.509**.

   d. Clique em **Salvar**

### <a name="create-wedo-test-user"></a>Criar um usuário de teste do WEDO

Nesta seção, você criará um usuário de teste no WEDO chamado Mateus	Rodrigues. As informações precisam corresponder àquelas indicadas em *Criar um usuário de teste do Azure AD*.

1. Na configuração de Perfil no WEDO, selecione **Usuários** na seção *Configurações de rede*.
1. Clique em **Adicionar usuário**.
1. No pop-up Adicionar usuário, preencha as informações do usuário

    a. Nome `B`.

    b. Sobrenome `Simon`.

    c. Insira o email `username@companydomain.extension`. Por exemplo, `B.Simon@contoso.com`. É obrigatório ter um email com o mesmo domínio do nome curto da sua empresa.

    d. Tipo de usuário `User`.

    e. Clique em **Criar usuário**.

    f. Na página *Selecionar equipes*, clique em **Salvar**.

    g.  Na página *Convidar usuário*, clique em **Sim**.

1. Validar o usuário usando o link recebido por email

> [!NOTE]
> Caso deseje criar um usuário fictício (o email acima não existe em sua rede), contate [nosso suporte](mailto:info@wedo.swiss) para validar o usuário*.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do WEDO no Painel de Acesso, você deverá ser conectado automaticamente ao WEDO, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o WEDO com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o WEDO com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Coda | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Coda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.openlocfilehash: c78744538991e337b226e49a053807ef8174d507
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455724"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Coda

Neste tutorial, você aprenderá a integrar o Coda ao Azure AD (Azure Active Directory). Ao integrar o Coda ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Coda.
* Permitir que os usuários sejam conectados automaticamente ao Coda com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura (Enterprise) habilitada para SSO (logon único) do Coda com a integração do GDrive desabilitada. Contate a [equipe de suporte do Coda](mailto:support@coda.io) para desabilitar a integração do GDrive para a sua organização se ela estiver habilitada no momento.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Coda dá suporte ao SSO iniciado por **IdP**

* O Coda dá suporte ao provisionamento de usuário **Just-In-Time**

* Depois de configurar o Coda, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Como adicionar o Coda por meio da galeria

Para configurar a integração do Coda ao Azure AD, você precisará adicionar o Coda por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Coda** na caixa de pesquisa.
1. Selecione **Coda** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Configurar e testar o logon único do Azure AD para o Coda

Configure e teste o SSO do Azure AD com o Coda usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Coda.

Para configurar e testar o SSO do Azure AD com o Coda, conclua os seguintes blocos de construção:

1. **[Iniciar a configuração do SSO do Coda](#begin-configuration-of-coda-sso)** : para iniciar a configuração do SSO no Coda.
1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
   * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
   * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Coda](#configure-coda-sso)** : para concluir a configuração das definições de logon único no Coda.
   * **[Criar um usuário de teste do Coda](#create-coda-test-user)**: para ter um equivalente de B.Fernandes no Coda que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="begin-configuration-of-coda-sso"></a>Iniciar a configuração do SSO do Coda

Siga estas etapas no Coda como introdução.

1. No Coda, abra o painel **Configurações da organização**.

   ![Abrir as configurações da organização](media/coda-tutorial/org-settings.png)

1. Verifique se a sua organização tem a integração do GDrive desativada. Se ela estiver habilitada no momento, contate a [equipe de suporte do Coda](mailto:support@coda.io) para ajudar você a fazer a migração do GDrive.

   ![GDrive desabilitado](media/coda-tutorial/gdrive-off.png)

1. Em **Autenticar com o SSO (SAML)** , selecione a opção **Configurar SAML**.

   ![Configurações do SAML](media/coda-tutorial/saml-settings-link.png)

1. Anote os valores da **ID da Entidade** e da **URL de Resposta SAML**, as quais você precisará nas etapas seguintes.

   ![ID da Entidade e URL de Resposta SAML a serem usadas no Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Coda**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

   a. Na caixa de texto **Identificador**, insira a "ID da Entidade" indicada acima. Ela deverá seguir o padrão: `https://coda.io/samlId/<CUSTOMID>`

   b. Na caixa de texto **URL de Resposta**, insira a "URL de Resposta SAML" indicada acima. Ela deverá seguir o padrão: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Seus valores serão diferentes dos mostrados acima; encontre os valores no console "Configurar SAML" do Coda. Atualize esses valores com o Identificador e a URL de Resposta reais.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Coda**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Coda.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Coda**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

   ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-coda-sso"></a>Configurar o SSO do Coda

Para concluir a instalação, você inserirá valores do Azure Active Directory no painel **Configurar SAML** do Coda.

1. No Coda, abra o painel **Configurações da organização**.
1. Em **Autenticar com o SSO (SAML)** , selecione a opção **Configurar SAML**.
1. Defina **Provedor SAML** como **Azure Active Directory**.
1. Em **URL de Logon do Provedor de Identidade**, cole a **URL de Logon** do console do Azure.
1. Em **Emissor do Provedor de Identidade**, cole o **Identificador do Azure AD** do console do Azure.
1. Em **Certificado Público do Provedor de Identidade**, selecione a opção **Carregar Certificado** e selecione o arquivo de certificado baixado anteriormente.
1. Clique em **Salvar**.

Isso conclui o trabalho necessário para a configuração da conexão SSO do SAML.

### <a name="create-coda-test-user"></a>Criar um usuário de teste do Coda

Nesta seção, um usuário chamado B.Fernandes será criado no Coda. O Coda dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Coda, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Coda no Painel de Acesso, você deverá ser conectado automaticamente ao Coda, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Coda com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Coda com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
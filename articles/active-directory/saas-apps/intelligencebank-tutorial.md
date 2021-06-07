---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o IntelligenceBank | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o IntelligenceBank.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: 9887c435c2aa8dc7ba8cab9481cf30195c4b334e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459927"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-intelligencebank"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o IntelligenceBank

Neste tutorial, você aprenderá a integrar o IntelligenceBank ao Azure AD (Azure Active Directory). Ao integrar o IntelligenceBank no Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao IntelligenceBank.
* Permitir que os usuários sejam conectados automaticamente ao IntelligenceBank com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do IntelligenceBank.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O IntelligenceBank é compatível com o SSO iniciado por **SP**

* Depois de configurar o IntelligenceBank, é possível impor o controle de sessão, que protege contra o vazamento e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-intelligencebank-from-the-gallery"></a>Como adicionar o IntelligenceBank da galeria

Para configurar a integração do IntelligenceBank ao Azure AD, você precisa adicionar o IntelligenceBank por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **IntelligenceBank** na caixa de pesquisa.
1. Escolha **IntelligenceBank** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-intelligencebank"></a>Configurar e testar logon único do Azure AD para o IntelligenceBank

Configure e teste o SSO do Azure AD com o IntelligenceBank usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do IntelligenceBank.

Para configurar e testar o SSO do Azure AD com o IntelligenceBank, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do IntelligenceBank](#configure-intelligencebank-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do IntelligenceBank](#create-intelligencebank-test-user)** – para ter um equivalente de B.Fernandes no IntelligenceBank que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **IntelligenceBank**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.intelligencebank.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , use uma das seguintes opções:

    - `IB`
    - `IntelligenceBank`
    - `https://<SUBDOMAIN>.intelligencebank.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.intelligencebank.com/auth`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do IntelligenceBank](mailto:helpdesk@intelligencebank.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o IntelligenceBank**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao IntelligenceBank.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **IntelligenceBank**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-intelligencebank-sso"></a>Configurar o SSO do IntelligenceBank

1. Em outra janela do navegador da Web, entre em seu site de empresa do IntelligenceBank como administrador.

1. Clique em **Autenticador** e depois em **Adicionar Novo**

    ![A captura de tela mostra a guia Administrador selecionada e o ícone Adicionar Novo.](./media/intelligencebank-tutorial/authenticator.PNG)

1. Execute as seguintes etapas:

    ![A captura de tela mostra os campos em que você insere as informações nesta etapa.](./media/intelligencebank-tutorial/urls.PNG)

    a. Na caixa de texto **Nome**, digite o nome, por exemplo, `azureadsso`.

    b. Na caixa de texto **Descrição**, insira uma descrição válida.

    c. Selecione **SAML** na lista suspensa como o **Tipo**.

    d. Na caixa de texto **URL Remota**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Na caixa de texto **Host**, cole o valor da **ID da Entidade** copiado do portal do Azure.

    f. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **CertData**.

    g. Na caixa de texto **SingleLogoutService**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    h. Clique no botão **Salvar**.

### <a name="create-intelligencebank-test-user"></a>Criar usuário de teste do IntelligenceBank

1. Em outra janela do navegador da Web, entre em seu site de empresa do IntelligenceBank como administrador.

1. Acesse **Admin** -> **Usuários** e selecione o **Ícone Adicionar Novo Usuário** para adicionar o **Usuário**.

    ![A captura de tela mostra o ícone Usuários selecionado na guia Usuários.](./media/intelligencebank-tutorial/creating-user.PNG)

1. Preencha os campos necessários de acordo com os requisitos da sua organização e clique em **Salvar**.

    ![A captura de tela mostra a página Adicionar Novo Usuário em que você insere as informações do usuário.](./media/intelligencebank-tutorial/creating-user-1.PNG)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do IntelligenceBank no Painel de Acesso, você será conectado automaticamente ao aplicativo IntelligenceBank para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o IntelligenceBank com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o IntelligenceBank com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
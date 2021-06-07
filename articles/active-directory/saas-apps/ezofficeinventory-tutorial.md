---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EZOfficeInventory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EZOfficeInventory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: dcf64e6538140905e00e0fcd9d0383f15e8ad95a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453649"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EZOfficeInventory

Neste tutorial, você aprenderá a integrar o EZOfficeInventory ao Azure AD (Azure Active Directory). Quando integrar o EZOfficeInventory ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao EZOfficeInventory.
* Permitir que seus usuários entrem automaticamente no EZOfficeInventory com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do EZOfficeInventory com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EZOfficeInventory dá suporte ao SSO iniciado por **SP**
* O EZOfficeInventory dá suporte ao provisionamento de usuário **Just-In-Time**
* Depois de configurar o EZOfficeInventory, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>Adicionar o EZOfficeInventory da galeria

Para configurar a integração do EZOfficeInventory ao Azure AD, é necessário adicionar o EZOfficeInventory da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **EZOfficeInventory** na caixa de pesquisa.
1. Selecione **EZOfficeInventory** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Configurar e testar logon único do Azure AD para o EZOfficeInventory

Configure e teste o SSO do Azure AD com o EZOfficeInventory usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no EZOfficeInventory.

Para configurar e testar o SSO do Azure AD com o EZOfficeInventory, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do EZOfficeInventory](#configure-ezofficeinventory-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do EZOfficeInventory](#create-ezofficeinventory-test-user)** – para ter um equivalente do usuário B.Fernandes no EZOfficeInventory que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EZOfficeInventory**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte do cliente EZOfficeInventory](mailto:support@ezofficeinventory.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo EZOfficeInventory espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo EZOfficeInventory espera que mais alguns atributos sejam passados novamente na resposta SAML que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de Origem|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | email | user.mail |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar EZOfficeInventory**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao EZOfficeInventory.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **EZOfficeInventory**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ezofficeinventory-sso"></a>Configurar o SSO do EZOfficeInventory

1. Para automatizar a configuração no EZOfficeInventory, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clicar em **Configurar o EZOfficeInventory** o levará para o aplicativo EZOfficeInventory. De lá, forneça as credenciais de administrador para entrar no EZOfficeInventory. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

1. Se desejar configurar o EZOfficeInventory manualmente, abra uma nova janela do navegador da Web, entre no site da empresa EZOfficeInventory como administrador e execute as seguintes etapas:

1. No canto superior direito da página, clique em **Perfil** e navegue para **Configurações** > **Suplementos**.

    ![Captura de tela que mostra a página "Configurações" com a ação "Complementos" selecionada.](./media/ezofficeinventory-tutorial/configure01.png)

1. Role para baixo até a seção **Integração do SAML** e execute as seguintes etapas:

    ![Configuração do EZOfficeInventory](./media/ezofficeinventory-tutorial/configure02.png)

    a. Marque a opção **Habilitado**.

    b. Na caixa de texto **URL do Provedor de Identidade**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra o certificado codificado em Base64 no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Certificado do Provedor de Identidade**.

    d. Na caixa de texto **Texto do botão de logon**, digite o texto do botão de logon.

    e. Na caixa de texto **Nome**, insira **first_name**.

    f. Na caixa de texto **Sobrenome**, insira **last_name**.

    g. Na caixa de texto **Email**, insira o **email**.

    h. Selecione sua função de acordo com seu requisito da opção **Função por padrão do EZOfficeInventory**.

    i. Clique em **Atualizar**.

### <a name="create-ezofficeinventory-test-user"></a>Criar usuário de teste do EZOfficeInventory

Nesta seção, uma usuária chamada Brenda Fernandes será criada no EZOfficeInventory. O EZOfficeInventory dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no EZOfficeInventory, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do EZOfficeInventory no Painel de Acesso, você deverá ser conectado automaticamente ao EZOfficeInventory para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Experimentar o EZOfficeInventory com o Azure AD](https://aad.portal.azure.com/)
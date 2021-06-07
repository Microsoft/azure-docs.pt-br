---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao askSpoke | Microsoft Docs'
description: Aprenda a configurar o logon único entre o Azure Active Directory e o askSpoke.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 8ef2dadfc0099822d68a5b1cfb2cc35759e1a866
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92457649"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askspoke"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao askSpoke

Neste tutorial, você aprenderá a integrar o askSpoke ao Azure AD (Azure Active Directory). Ao integrar o askSpoke ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao askSpoke.
* Permitir que seus usuários sejam automaticamente conectados ao askSpoke com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do askSpoke habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O askSpoke dá suporte ao SSO iniciado por **SP e IDP**
* O askSpoke é compatível com o provisionamento de usuário **just-in-time**
* Depois de configurar o askSpoke, você poderá impor o controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-askspoke-from-the-gallery"></a>Adicionar o askSpoke da galeria

Para configurar a integração do askSpoke ao Azure AD, você precisará adicionar o askSpoke da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **askSpoke** na caixa de pesquisa.
1. Selecione **askSpoke** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-askspoke"></a>Configurar e testar o SSO do Azure AD para askSpoke

Configure e teste o SSO do Azure AD com o askSpoke usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do askSpoke.

Para configurar e testar o SSO do Azure AD com o askSpoke, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do askSpoke](#configure-askspoke-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do askSpoke](#create-askspoke-test-user)** – para ter um equivalente de B.Fernandes no askSpoke que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **askSpoke**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.askspoke.com/saml/callback`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize esse valor com a URL de Resposta real. Entre em contato com a [equipe de suporte ao Cliente do askSpoke](mailto:support@askspoke.com) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://askspoke.com/login`

1. O aplicativo askSpoke espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo askSpoke espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de Origem|
    | ---------------| --------- |
    | firstName | user.givenname |
    | lastName | user.surname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o askSpoke**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao askSpoke.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **askSpoke**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-askspoke-sso"></a>Configurar o SSO do askSpoke

1. Em outra janela do navegador da Web, entre no site do askSpoke como administrador.

1. Clique na guia **Configurações** do painel de navegação esquerdo.

    ![Guia de configurações do askSpoke](./media/askspoke-tutorial/configure1.png)

1. Role para baixo até **SSO** e clique em **Conectar**.

    ![Conexão do askSpoke](./media/askspoke-tutorial/configure2.png)

1. Na seção **Habilitar SAML e SCIM**, execute as seguintes etapas:

    ![Seção Habilitar SAML & SCIM do askSpoke](./media/askspoke-tutorial/configure3.png)

    1. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    1. Na caixa de texto **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    1. Abra o arquivo **Certificate(Base64)** baixado do portal do Azure no Bloco de notas, copie o conteúdo do certificado e cole-o na caixa de texto **Certificado público**.

    1. Copie o valor da **URL do ACS** e cole-o na caixa de texto **URL de Resposta** da seção **Configuração Básica do SAML** no portal do Azure.

    1. Clique em **Testar conectividade SAML**.

### <a name="create-askspoke-test-user"></a>Criar um usuário de teste do askSpoke

Nesta seção, um usuário chamado B.Fernandes será criado no askSpoke. O askSpoke é compatível com o provisionamento just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no askSpoke, um será criado quando você tentar acessá-lo.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do askSpoke no Painel de Acesso, você deverá ser conectado automaticamente ao askSpoke, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o askSpoke com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
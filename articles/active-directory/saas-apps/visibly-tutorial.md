---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Visibly | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Visibly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: jeedes
ms.openlocfilehash: de41e1ef021ea3da2b3a5bef4268469aab448a21
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-visibly"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Visibly

Neste tutorial, você aprenderá a integrar o Visibly ao Azure AD (Azure Active Directory). Ao integrar o Visibly ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Visibly.
* Permitir que os usuários sejam conectados automaticamente ao Visibly com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Visibly habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Visibly dá suporte ao SSO iniciado por **SP**

* Depois de configurar o Visibly, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-visibly-from-the-gallery"></a>Como adicionar o Visibly por meio da galeria

Para configurar a integração do Visibly ao Azure AD, você precisará adicionar o Visibly por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria** , digite **Visibly** na caixa de pesquisa.
1. Escolha **Visibly** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-visibly"></a>Configurar e testar o SSO do Azure AD para o Visibly

Configure e teste o SSO do Azure AD com o Visibly usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Visibly.

Para configurar e testar o SSO do Azure AD com o Visibly, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Visibly](#configure-visibly-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Visibly](#create-visibly-test-user)** : para ter um equivalente de B.Fernandes no Visibly que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Visibly** , localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único** , escolha **SAML**.
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon** , digite a URL: `https://app.visibly.io/`

    b. Na caixa de texto **URL de Resposta** , digite a URL: `https://api.visibly.io/api/v1/verifyResponse`


1. O aplicativo Visibly espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Visibly espera que mais alguns atributos sejam transmitidos novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ----------- | --------- |
    | city | user.city |
    | lastName | user.surname |
    | state | user.state |
    | department | user.department |
    | email | user.mail |

1. Na página **Configurar o logon único com o SAML** , na seção **Certificado de Autenticação SAML** , localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Visibly** , copie as URLs apropriadas de acordo com as suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** , **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Visibly.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Visibly**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir**.

## <a name="configure-visibly-sso"></a>Configurar o SSO do Visibly

1. Faça logon no Visibly usando suas credenciais.

1. Procure a opção **Configurações** no menu de navegação.

    ![A captura de tela mostra a opção Configurações selecionada.](./media/visibly-tutorial/settings.png)

1. Clique em **Integrações** em Configurações.

    ![A captura de tela mostra a opção Integrações selecionada no menu Configurações.](./media/visibly-tutorial/integrations.png)

1. Em **Integrações** , selecione **SSO**.

    ![A captura de tela mostra a opção SSO selecionada em Integrações.](./media/visibly-tutorial/sso.png)

1. Execute as etapas abaixo na página a seguir.

    ![Captura de tela que mostra a página Integrações de SSO, na qual você pode inserir os valores descritos.](./media/visibly-tutorial/configuration.png)

    a. Na caixa de texto **ID da Entidade** , cole o valor da **ID da Entidade** copiado do portal do Azure.

    b. Na caixa de texto **URL de SSO** , cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **Nome do SSO** , forneça qualquer nome válido.

    d. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo dele na caixa de texto **Certificado** ou carregue também o **Certificado** selecionando **Carregar Certificado**.

    e. Clique em **Salvar**

### <a name="create-visibly-test-user"></a>Criar um usuário de teste do Visibly

Nesta seção, um usuário chamado B.Fernandes será criado no Visibly. O Visibly suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Visibly, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Visibly no Painel de Acesso, você deverá ser conectado automaticamente ao Visibly, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Visibly com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
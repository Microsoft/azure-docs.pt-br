---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao edX for Business SAML Integration | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o edX for Business SAML Integration.
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
ms.openlocfilehash: b9bf27ccb390ee41306215a90fccb01747077837
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454228"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-edx-for-business-saml-integration"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory integração com o edX for Business SAML Integration

Neste tutorial, você aprenderá a integrar o edX for Business SAML Integration ao Azure AD (Azure Active Directory). Ao integrar o edX for Business SAML Integration ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao edX for Business SAML Integration.
* Permitir que seus usuários sejam conectados automaticamente ao edX for Business SAML Integration com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do edX for Business SAML Integration.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.
* O edX for Business SAML Integration é compatível com SSO iniciado pelo **SP**
* O edX for Business SAML Integration é compatível com provisionamento de usuário **Just-in-Time**

* Depois de configurar o edX for Business SAML Integration, você poderá impor o controle de sessão, que protege contra a exfiltração e a infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-edx-for-business-saml-integration-from-the-gallery"></a>Como adicionar o edX for Business SAML Integration da galeria

Para configurar o edX for Business SAML Integration no Azure AD, você precisa adicionar o edX for Business SAML Integration da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory** .
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos** .
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** .
1. Na seção **Adicionar da galeria** , digite **edX for Business SAML Integration** na caixa de pesquisa.
1. Selecione **edX for Business SAML Integration** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-edx-for-business-saml-integration"></a>Configurar e testar o SSO do Azure AD para o edX for Business SAML Integration

Configure e teste o SSO do Azure AD com o edX for Business SAML Integration usando um usuário de teste chamado **B.Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do edX for Business SAML Integration.

Para configurar e testar o SSO do Azure AD com o edX for Business SAML Integration, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do edX for Business SAML Integration](#configure-edx-for-business-saml-integration-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do edX for Business SAML Integration](#create-edx-for-business-saml-integration-test-user)** – para ter um equivalente de B.Fernandes no edX for Business SAML Integration que esteja vinculado à declaração do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **edX for Business SAML Integration** , localize a seção **Gerenciar** e escolha **Logon único** .
1. Na página **Selecionar um método de logon único** , escolha **SAML** .
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite um URL usando o seguinte padrão: `https://courses.edx.org/dashboard?tpa_hint=<INSTANCE_NAME>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate [equipe de suporte ao cliente do edX for Business SAML Integration](mailto:api-support@edx.org) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo edX for Business SAML Integration espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo edX for Business SAML Integration espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome | Atributo de Origem|
    | ---------------| --------- |
    | country | user.country  |

1. Na página **Configurar o logon único com o SAML** , na seção **Certificado de Autenticação SAML** , clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)
### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** , **Usuários** e, em seguida, **Todos os usuários** .
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha** .
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao edX for Business SAML Integration.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos** .
1. Na lista de aplicativos, selecione **edX for Business SAML Integration** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

## <a name="configure-edx-for-business-saml-integration-sso"></a>Configurar o SSO do edX for Business SAML Integration

Para configurar o logon único no lado do **edX for Business SAML Integration** , você precisa enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do edX for Business SAML Integration](mailto:api-support@edx.org). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-edx-for-business-saml-integration-test-user"></a>Criar o usuário de teste do edX for Business SAML Integration

Nesta seção, um usuário chamado Brenda Fernandes é criado no edX for Business SAML Integration. O edX for Business SAML Integration é compatível com provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no edX for Business SAML Integration, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco edX for Business SAML Integration no painel de acesso, deve ser automaticamente conectado ao edX for Business SAML Integration para o qual você configura o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Avaliar o edX for Business SAML Integration com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o edX for Business SAML Integration com controles e visibilidade avançados](/cloud-app-security/proxy-intro-aad)
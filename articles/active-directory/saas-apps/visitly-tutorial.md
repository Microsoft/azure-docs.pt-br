---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Visitly | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Visitly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: cc37c39c89996213f7c13cc105ea697945d25361
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519292"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-visitly"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Visitly

Neste tutorial, você aprenderá como integrar o Visitly ao Azure AD (Azure Active Directory). Quando integrar o Visitly ao Azure AD, você poderá:

* Controlar no Azure AD quem terá acesso ao Visitly.
* Permitir que os usuários sejam conectados automaticamente ao Visitly com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Visitly.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Visitly é compatível com SSO iniciado por **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-visitly-from-the-gallery"></a>Adição do Visitly por meio da galeria

Para configurar a integração do Visitly ao Azure AD, você precisa adicionar o Visitly da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory** .
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos** .
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** .
1. Na seção **Adicionar por meio da galeria** , digite **Visitly** na caixa de pesquisa.
1. Selecione **Visitly** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-visitly"></a>Configurar e testar o logon único do Azure AD para o Visitly

Configure e teste o SSO do Azure AD com o Visitly usando um usuário de teste com o nome **B.Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Visitly.

Para configurar e testar o SSO do Azure AD com o Visitly, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Visitly](#configure-visitly-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Visitly](#create-visitly-test-user)** – para ter um equivalente de B.Fernandes no Visitly vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Visitly** , localize a seção **Gerenciar** e selecione **logon único** .
1. Na página **Selecionar um método de logon único** , escolha **SAML** .
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML** , o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar** .

1. O aplicativo Visitly espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Visitly espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem |
    | ---------------| --------------- |
    | Valor do identificador do nome  | user.mail |

1. Na página **Configurar o logon único com o SAML** , na seção **Certificado de Autenticação SAML** , localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Visitly** , copie as URLs apropriadas de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Visitly.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos** .
1. Na lista de aplicativos, escolha **Visitly** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

## <a name="configure-visitly-sso"></a>Configurar o SSO do Visitly

Para configurar o logon único no lado do **Visitly** , é necessário enviar o **XML de metadados de federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Visitly](mailto:support@visitly.io). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-visitly-test-user"></a>Criar usuário de teste do Visitly

Nesta seção, você criará uma usuária chamado Brenda Fernandes no Visitly. Trabalhe com a [equipe de suporte do Visitly](mailto:support@visitly.io) para adicionar os usuários à plataforma do Visitly. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Visitly no Painel de Acesso, você deverá ser conectado automaticamente ao Visitly para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Visitly com o Azure AD](https://aad.portal.azure.com/)
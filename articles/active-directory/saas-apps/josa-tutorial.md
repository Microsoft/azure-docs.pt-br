---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao JOSA | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o JOSA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/08/2020
ms.author: jeedes
ms.openlocfilehash: 290d70e4dc83a4db0b1c0c31e9d60a32d124dc75
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459315"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-josa"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao JOSA

Neste tutorial, você aprenderá como integrar o JOSA ao Azure AD (Azure Active Directory). Ao integrar o JOSA ao Azure AD, você poderá:

* Controlar quem tem acesso ao JOSA no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao JOSA com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do JOSA.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O JOSA é compatível com o SSO iniciado por **SP**

## <a name="adding-josa-from-the-gallery"></a>Adicionar o JOSA da galeria

Para configurar a integração do JOSA ao Azure AD, você precisa adicionar o JOSA da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria** , digite **JOSA** na caixa de pesquisa.
1. Selecione **JOSA** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-josa"></a>Configurar e testar o logon único do Azure AD para o JOSA

Configure e teste o SSO do Azure AD com o JOSA usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do JOSA.

Para configurar e testar o SSO do Azure AD com o JOSA, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do JOSA](#configure-josa-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do JOSA](#create-josa-test-user)** – para ter um equivalente de B.Fernandes no JOSA que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **JOSA** , localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único** , escolha **SAML**.
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML** , se você tiver um **arquivo de metadados do provedor de serviços** , execute as seguintes etapas:

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, o valor de **Identificador** será populado automaticamente na seção Configuração Básica do SAML.

    Na caixa de texto **URL de Logon** , digite a URL: `https://www.jo-sa.dk/adfslogin.php`

    > [!Note]
    > Se o valor de **Identificador** não for preenchido de modo automático, preencha o valor manualmente de acordo com suas necessidades. O valor da URL de logon não é real. Atualize esse valor com a URL de Entrada real. Entre em contato com a [equipe de suporte ao cliente do JOSA](mailto:hr@alldialogue.com) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML** , na seção **Certificado de Autenticação SAML** , clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao JOSA.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **JOSA**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir**.

## <a name="configure-josa-sso"></a>Configurar o SSO do JOSA

Para configurar o logon único no lado do **JOSA** , é necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do JOSA](mailto:hr@alldialogue.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-josa-test-user"></a>Criar usuário de teste do JOSA

Nesta seção, você criará uma usuária chamada B.Fernandes no JOSA. Trabalhe com a [equipe de suporte do JOSA](mailto:hr@alldialogue.com) para adicionar os usuários na plataforma do JOSA. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do JOSA no Painel de Acesso, você deverá ser conectado automaticamente ao JOSA para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o JOSA com o Azure AD](https://aad.portal.azure.com/)
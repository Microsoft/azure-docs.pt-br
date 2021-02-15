---
title: 'Tutorial: Integração do Azure Active Directory com o Expensify | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Expensify.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.openlocfilehash: 23998a46db21173598ad01954d36027c4fd97935
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453739"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Tutorial: Integrar o Expensify ao Azure Active Directory

Neste tutorial, você aprenderá como integrar o Expensify ao Azure Active Directory (Azure AD). Ao integrar o Expensify ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Expensify.
* Permitir que seus usuários entrem automaticamente no Expensify com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Expensify habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Expensify é compatível com o SSO iniciado por **SP**

## <a name="adding-expensify-from-the-gallery"></a>Adicionando o Expensify da galeria

Para configurar a integração do Expensify ao Azure AD, você precisa adicionar o Expensify da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory** .
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos** .
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** .
1. Na seção **Adicionar por meio da galeria** , digite **Expensify** na caixa de pesquisa.
1. Selecione **Expensify** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-expensify"></a>Configurar e testar o logon único do Azure AD para o Expensify

Configure e teste o SSO do Azure AD com o Expensify usando um usuário de teste chamado **B. Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Expensify.

Para configurar e testar o SSO do Azure AD com o Expensify, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Expensify](#configure-expensify-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Expensify](#create-expensify-test-user)** – para ter um equivalente de B.Fernandes no Expensify que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Expensify** , localize a seção **Gerenciar** e selecione **Logon único** .
1. Na página **Escolher um método de logon único** , escolha **SAML** .
1. Na página **Configurar o Logon Único com SAML** , clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL: `https://www.expensify.com/authentication/saml/login`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL: `https://www.expensify.com`

    c. b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize esse valor com a URL de Resposta real. Contate a [equipe de suporte ao Cliente do Expensify](mailto:help@expensify.com) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML** , na seção **Certificado de Assinatura SAML** , localize **XML de Metadados** e selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Expensify** , copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao Expensify.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos** .
1. Na lista de aplicativos, selecione **Expensify** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

## <a name="configure-expensify-sso"></a>Configurar o SSO do Expensify

Para habilitar o SSO no Expensify, primeiro habilite o **Controle de Domínio** no aplicativo. Você pode habilitar o Controle de Domínio no aplicativo por meio das etapas listadas [aqui](https://help.expensify.com/domain-control). Para obter suporte adicional, trabalhe com a [equipe de suporte ao Cliente do Expensify](mailto:help@expensify.com). Depois que o Controle de Domínio estiver habilitado, siga estas etapas:

![Configurar o logon único](./media/expensify-tutorial/tutorial_expensify_51.png)

1. Faça logon no seu aplicativo Expensify.

2. No painel esquerdo, clique em **Configurações** e navegue para **SAML** .

3. Alterne a opção **Logon do SAML** para **habilitado** .

4. Abra os Metadados de Federação baixados do Azure AD no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Metadados do Provedor de Identidade** .

### <a name="create-expensify-test-user"></a>Criar usuário de teste do Expensify

Nesta seção, você criará um usuário chamado B.Fernandes no Expensify. Trabalhe com a [equipe de suporte ao Cliente do Expensify](mailto:help@expensify.com) para adicionar usuários na plataforma do Expensify.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Expensify no Painel de Acesso, você deverá ser conectado automaticamente ao Expensify, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
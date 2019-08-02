---
title: 'Tutorial: Integração do Azure Active Directory ao Predictix Ordering | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o logon único entre o Azure Active Directory e o Predictix Ordering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2fe2f976-e97f-4368-9695-3e1624409e8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 457ab3a0d5e816becbd2b32d858d5172951f27ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094133"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Tutorial: Integração do Azure Active Directory com o Predictix Ordering

Neste tutorial, você aprenderá como integrar o Predictix Ordering ao Azure AD (Azure Active Directory).
Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao Predictix Ordering.
* Você pode permitir que seus usuários sejam automaticamente conectados ao Predictix Ordering (logon único) com as respectivas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Predictix Ordering, você precisa ter:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do Predictix Ordering com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Predictix Ordering é compatível com SSO iniciado por SP.

## <a name="add-predictix-ordering-from-the-gallery"></a>Adicionar o Predictix Ordering da galeria

Para configurar a integração do Predictix Ordering ao Azure AD, você precisa adicionar o Predictix Ordering da galeria à sua lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**:

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Predictix Ordering**. Selecione **Predictix Ordering** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Predictix Ordering, com base em um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no Predictix Ordering.

Para configurar e testar o logon único do Azure AD com o Predictix Ordering, você precisará concluir estas etapas:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do Predictix Ordering](#configure-predictix-ordering-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Criar um usuário de teste do Predictix Ordering](#create-a-predictix-ordering-test-user)** que esteja vinculado à representação do usuário do Azure AD.
6. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Predictix Ordering, execute estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Predictix Ordering**, selecione **Logon único**:

    ![Selecione Logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica do SAML**, conclua as etapas a seguir.

    ![Caixa de diálogo Configuração Básica de SAML](common/sp-identifier.png)

    1. Na caixa **URL de Logon**, insira uma URL neste padrão:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. Na caixa **Identificador (ID da Entidade)** , insira uma URL neste padrão:

        | |
        |--|
        | `https://<companyname-pricing>.dev.ordering.predictix.com` |
        | `https://<companyname-pricing>.ordering.predictix.com` |
        | |

    > [!NOTE]
    > Esses valores são espaços reservados. Você precisa usar o identificador e a URL de logon real. Contate a [equipe de suporte do Predictix Ordering](https://www.predix.io/support/) para obter os valores. Você também pode ver os padrões exibidos na caixa de diálogo **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado do **Certificado (Base64)** , de acordo com suas necessidades e salve o certificado no computador:

    ![Link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar o Predictix Ordering**, copie as URLs apropriadas de acordo com suas necessidades:

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    2. **Identificador do Azure AD**.

    3. **URL de Logoff**.

### <a name="configure-predictix-ordering-single-sign-on"></a>Configurar logon único do Predictix Ordering

Para configurar o logon único no lado do Predictix Ordering, é necessário enviar o certificado baixado e as URLs copiadas do portal do Azure para a [equipe de suporte do Predictix Ordering](https://www.predix.io/support/). Essa equipe garante que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste chamado Brenda Fernandes no portal do Azure.

1. No portal do Azure, selecione **Azure Active Directory** no painel esquerdo, **Usuários** e, em seguida, **Todos os usuários**:

    ![Selecionar Todos os usuários](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela:

    ![Selecionar Novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, execute as etapas a seguir.

    ![Caixa de diálogo Usuário](common/user-properties.png)

    1. Na caixa **Nome**, insira **BrendaFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **BrendaFernandes@\<domíniodaempresa>.\<extensão>** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **Mostrar Senha** e, em seguida, anote o valor mostrado na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure AD concedendo-lhe acesso ao Predictix Ordering.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **Predictix Ordering**:

    ![Aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Predictix Ordering**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-predictix-ordering-test-user"></a>Criar um usuário de teste do Predictix Ordering

Em seguida, você precisará criar um usuário chamado Brenda Fernandes no Predictix Ordering. Trabalhe com a [equipe de suporte do Predictix Ordering](https://www.predix.io/support/) para adicionar usuários. Os usuários precisam ser criados e ativados para que você use o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você seleciona o bloco Predictix Ordering no Painel de Acesso, deve ser conectado automaticamente à instância do Predictix Ordering para a qual você configurou o SSO. Para obter mais informações, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Tutorial: Integração do Azure Active Directory com Predictix Assortment Planning | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o logon único entre o Azure Active Directory e o Predictix Assortment Planning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 37e686ff-f8e5-40b1-9d7e-f64b076917b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: bc3ea2f6fddc233a69d96c0c885ab310ed1e77c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094161"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Tutorial: Integração do Azure Active Directory com Predictix Assortment Planning

Neste tutorial, você aprenderá a integrar o Predictix Assortment Planning ao Azure AD (Azure Active Directory).
Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao Predictix Assortment Planning.
* Você pode permitir que os usuários sejam conectados automaticamente ao Predictix Assortment Planning (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Predictix Assortment Planning, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do Predictix Assortment Planning com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Predictix Assortment Planning dá suporte ao SSO iniciado por SP.

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>Adicionar o Predictix Assortment Planning da galeria

Para configurar a integração do Predictix Assortment Planning ao Azure AD, você precisará adicionar o Predictix Assortment Planning por meio da galeria à lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**:

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Predictix Assortment Planning**. Selecione **Predictix Assortment Planning** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Predictix Assortment Planning, com base em um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no Predictix Assortment Planning.

Para configurar e testar o logon único do Azure AD com o Predictix Assortment Planning, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do Predictix Assortment Planning](#configure-predictix-assortment-planning-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Criar um usuário de teste do Predictix Assortment Planning](#create-a-predictix-assortment-planning-test-user)** que esteja vinculado à representação do usuário do Azure AD.
6. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Predictix Assortment Planning, execute estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Predictix Assortment Planning**, selecione **Logon único**:

    ![Selecione Logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica do SAML**, conclua as etapas a seguir.

    ![Caixa de diálogo Configuração Básica de SAML](common/sp-identifier.png)

    1. Na caixa **URL de Logon**, insira uma URL neste padrão:

       | |
        |--|
        | `https://<sub-domain>.ap.predictix.com/sso/request`|
        | `https://<sub-domain>.dev.ap.predictix.com/`|
        | |

    1. Na caixa **Identificador (ID da Entidade)** , insira uma URL neste padrão:

        | |
        |--|
        | `https://<sub-domain>.ap.predictix.com`|
        | `https://<sub-domain>.dev.ap.predictix.com`|
        | |

    > [!NOTE]
    > Esses valores são espaços reservados. Você precisa usar o identificador e a URL de logon real. Contate a [equipe de suporte do Predictix Assortment Planning](https://www.infor.com/support) para obter os valores. Você também pode ver os padrões exibidos na caixa de diálogo **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado do **Certificado (Base64)** , de acordo com suas necessidades e salve o certificado no computador:

    ![Link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar o Predictix Assortment Planning**, copie as URLs apropriadas de acordo com suas necessidades:

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    1. **Identificador do Azure AD**.

    1. **URL de Logoff**.

### <a name="configure-predictix-assortment-planning-single-sign-on"></a>Configurar o logon único do Predictix Assortment Planning

Para configurar o logon único no lado do Predictix Assortment Planning, é necessário enviar o certificado baixado e as URLs copiadas do portal do Azure para a [equipe de suporte do Predictix Assortment Planning](https://www.infor.com/support). Essa equipe garante que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

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

    1. Selecione **Mostrar senha** e, em seguida, anote o valor mostrado na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure AD concedendo a ela acesso ao Predictix Assortment Planning.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **Predictix Assortment Planning**.

    ![Aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Predictix Assortment Planning**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-predictix-assortment-planning-test-user"></a>Criar um usuário de teste do Predictix Assortment Planning

Em seguida, você precisará criar um usuário chamado Brenda Fernandes no Predictix Assortment Planning. Trabalhe com a [equipe de suporte do Predictix Assortment Planning](https://www.infor.com/support) para adicionar usuários. Os usuários precisam ser criados e ativados para que você use o logon único.

> [!NOTE]
> O titular da conta do Azure AD recebe um email e seleciona um link para confirmar a conta a fim de torná-la ativa.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Predictix Assortment Planning no Painel de Acesso, você deverá ser conectado automaticamente à instância do Predictix Assortment Planning, para a qual você configurou o SSO. Para obter mais informações, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
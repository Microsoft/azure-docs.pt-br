---
title: 'Tutorial: Integração do Azure Active Directory ao RolePoint | Microsoft Docs'
description: Neste tutorial você aprenderá a configurar o logon único entre o Azure Active Directory e o RolePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 0b6fd17d2f8577532778733866260f43e9ac7685
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092725"
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>Tutorial: Integração do Azure Active Directory ao RolePoint

Neste tutorial, você aprenderá a integrar o RolePoint ao Azure AD (Azure Active Directory).
Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao RolePoint.
* Você pode permitir que os usuários sejam conectados automaticamente ao RolePoint (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao RolePoint, você precisará ter:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do RolePoint com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O RolePoint é compatível com o SSO iniciado por SP.

## <a name="add-rolepoint-from-the-gallery"></a>Adicionar o RolePoint da Galeria

Para configurar a integração do RolePoint ao Azure AD, você precisará adicionar o RolePoint por meio da galeria à lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**:

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **RolePoint**. Selecione **RolePoint** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o RolePoint usando um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no RolePoint.

Para configurar e testar o logon único do Azure AD com o RolePoint, você precisará concluir estas etapas:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do RolePoint](#configure-rolepoint-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Criar um usuário de teste do RolePoint](#create-a-rolepoint-test-user)** que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o RolePoint, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do RolePoint, selecione **Logon único**:

    ![Selecionar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica do SAML**, execute as etapas a seguir.

    ![Caixa de diálogo Configuração Básica de SAML](common/sp-identifier.png)

    1. Na caixa **URL de Logon**, insira uma URL neste padrão:

       `https://<subdomain>.rolepoint.com/login`

    1. Na caixa **Identificador (ID da Entidade)** , insira uma URL neste padrão:

       `https://app.rolepoint.com/<instancename>`

    > [!NOTE]
    > Esses valores são espaços reservados. Você precisa usar o identificador e a URL de logon real. Sugerimos que você use um valor de cadeia de caracteres exclusivo no identificador. Contate a [equipe de suporte do RolePoint](mailto:info@rolepoint.com) para obter esses valores. Você também pode ver os padrões exibidos na caixa de diálogo **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado do **XML de Metadados de Federação** de acordo com suas necessidades e salve o arquivo no computador.

    ![Link de download do certificado](common/metadataxml.png)

6. Na seção **Configurar o RolePoint**, copie as URLs apropriadas de acordo com suas necessidades:

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    1. **Identificador do Azure AD**.

    1. **URL de Logoff**.


### <a name="configure-rolepoint-single-sign-on"></a>Configurar o logon único do RolePoint

Para configurar o logon único no lado do RolePoint, você precisará trabalhar com a [equipe de suporte do RolePoint](mailto:info@rolepoint.com). Envie à equipe o arquivo XML de Metadados de Federação e as URLs que você obteve no portal do Azure. Ela configurará o RolePoint para garantir que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste chamado Brenda Fernandes no portal do Azure.

1. No portal do Azure, selecione **Azure Active Directory** no painel esquerdo, **Usuários** e, em seguida, **Todos os usuários**:

    ![Selecionar Todos os usuários](common/users.png)

2. Selecione **Novo usuário** na parte superior da janela:

    ![Selecionar Novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, execute as etapas a seguir.

    ![Caixa de diálogo Usuário](common/user-properties.png)

    1. Na caixa **Nome**, insira **BrendaFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **BrendaFernandes@\<domíniodaempresa>.\<extensão>** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **Mostrar Senha** e, em seguida, anote o valor mostrado na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao RolePoint.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **RolePoint**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **RolePoint**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da janela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da janela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-rolepoint-test-user"></a>Criar um usuário de teste do RolePoint

Em seguida, você precisará criar um usuário chamado Brenda Fernandes no RolePoint. Trabalhe com a  [equipe de suporte do RolePoint](mailto:info@rolepoint.com) para adicionar usuários ao RolePoint. Os usuários precisam ser criados e ativados para que você use o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do RolePoint no Painel de Acesso, você deverá ser conectado automaticamente à instância do RolePoint, para a qual você configurou o SSO. Para obter mais informações sobre o Painel de Acesso, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

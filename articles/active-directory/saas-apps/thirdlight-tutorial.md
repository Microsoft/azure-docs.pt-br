---
title: 'Tutorial: integração do Azure Active Directory com o ThirdLight | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o logon único entre o Azure Active Directory e o ThirdLight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 448d46cd21a63488c4f567d5555fe6406fc0fa73
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089100"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Tutorial: integração do Azure Active Directory com o ThirdLight

Neste tutorial, você aprenderá como integrar o ThirdLight ao Azure AD (Azure Active Directory). Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao ThirdLight.
* Você pode permitir que os usuários sejam conectados automaticamente ao ThirdLight (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Se quiser saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ThirdLight, você precisa ter:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para logon único do ThirdLight que tenha o logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ThirdLight dá suporte ao SSO iniciado por SP.

## <a name="add-thirdlight-from-the-gallery"></a>Adicionar o ThirdLight da galeria

Para configurar a integração do ThirdLight ao Azure AD, você precisará adicionar o ThirdLight da galeria à sua lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**:

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **ThirdLight**. Selecione **ThirdLight** nos resultados da pesquisa e selecione **Adicionar**.

     ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ThirdLight usando um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no ThirdLight.

Para configurar e testar o logon único do Azure AD com o ThirdLight, você precisará concluir estas etapas:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do ThirdLight](#configure-thirdlight-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Criar um usuário de teste do ThirdLight](#create-a-thirdlight-test-user)** que esteja vinculado à representação do Azure AD do usuário.
6. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ThirdLight, execute estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do ThirdLight, selecione **Logon único**:

    ![Selecionar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica do SAML**, conclua as etapas a seguir.

    ![Caixa de diálogo Configuração Básica de SAML](common/sp-identifier.png)

    1. Na caixa **URL de Logon**, insira uma URL neste padrão:
    
          `https://<subdomain>.thirdlight.com/`

    1. Na caixa **Identificador (ID da Entidade)** , insira uma URL neste padrão:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Esses valores são espaços reservados. Você precisa usar o identificador e a URL de logon real. Contate a [equipe de suporte do ThirdLight](https://www.thirdlight.com/support) para obter os valores. Você também pode ver os padrões exibidos na caixa de diálogo **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado do **XML de Metadados de Federação** de acordo com suas necessidades e salve o arquivo no computador:

    ![Link de download do certificado](common/metadataxml.png)

6. Na seção **Configurar o ThirdLight**, copie as URLs adequadas, de acordo com suas necessidades:

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    1. **Identificador do Azure AD**.

    1. **URL de Logoff**.

### <a name="configure-thirdlight-single-sign-on"></a>Configurar o logon único do ThirdLight

1. Em uma nova janela do navegador da Web, entre em seu site de empresa do ThirdLight como administrador.

1. Acesse **Configuração** > **Administração do Sistema** > **SAML2**:

    ![Administração do Sistema](./media/thirdlight-tutorial/ic805843.png "Administração do Sistema")

1. Na seção de configuração do SAML2, execute as seguintes etapas.
  
    ![Seção de configuração do SAML2](./media/thirdlight-tutorial/ic805844.png "Seção de configuração do SAML2")

    1. Selecione **Habilitar Logon Único do SAML2**.

    1. Em **Fonte de metadados do IdP**, selecione **Carregar Metadados do IdP do XML**.

    1. Abra o arquivo de metadados que você baixou do portal do Azure na seção anterior. Copie o conteúdo do arquivo e cole-o na caixa **XML de Metadados do IdP**.

    1. Selecione **Salvar configurações do SAML2**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela o acesso ao ThirdLight.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **ThirdLight**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ThirdLight**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da janela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da janela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-thirdlight-test-user"></a>Criar um usuário de teste do ThirdLight

Para permitir que os usuários do Azure AD entrem no ThirdLight, você precisará adicioná-los ao ThirdLight. Você precisará adicioná-los manualmente.

Para criar uma conta de usuário, siga estas etapas:

1. Entre em seu site de empresa do ThirdLight como administrador.

1. Acesse a guia **Usuários**.

1. Selecione **Usuários e Grupos**.

1. Selecione **Adicionar novo Usuário**.

1. Insira o nome de usuário, um nome ou descrição e o endereço de Email de uma conta válida do Azure AD que você deseja provisionar. Escolha uma Predefinição ou Grupo de Novos Membros.

1. Selecione **Criar**.

> [!NOTE]
> Você pode usar qualquer ferramenta de criação de conta de usuário ou API fornecida pelo ThirdLight para provisionar as contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do ThirdLight no Painel de Acesso, você deverá ser conectado automaticamente à instância do ThirdLight, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Tutorial: Integração do Azure Active Directory com o ITRP | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o logon único entre o Azure Active Directory e o ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d44391624e29d2bdd182bb07452e0e8def2d1407
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656686"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integração do Azure Active Directory com o ITRP

Neste tutorial, você aprenderá a integrar o ITRP ao Azure AD (Azure Active Directory).
Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao ITRP.
* Você pode permitir que os usuários sejam conectados automaticamente ao ITRP (Logon Único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ITRP, você precisará ter:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do ITRP que tenha o logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ITRP dá suporte ao SSO iniciado por SP.

## <a name="add-itrp-from-the-gallery"></a>Adicionar o ITRP por meio da galeria

Para configurar a integração do ITRP ao Azure AD, você precisará adicionar o ITRP da galeria à sua lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**:

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ITRP**. Selecione **ITRP** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ITRP usando um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no ITRP.

Para configurar e testar o logon único do Azure AD com o ITRP, você precisará concluir estas etapas:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do ITRP](#configure-itrp-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Criar um usuário de teste do ITRP](#create-an-itrp-test-user)** que esteja vinculado à representação do Azure AD do usuário.
6. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ITRP, execute estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do ITRP, selecione **Logon único**:

    ![Selecionar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica do SAML**, execute as etapas a seguir.

    ![Caixa de diálogo Configuração Básica de SAML](common/sp-identifier.png)

    1. Na caixa **URL de Logon**, insira uma URL neste padrão:
    
       `https://<tenant-name>.itrp.com`

    1. Na caixa **Identificador (ID da Entidade)** , insira uma URL neste padrão:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Esses valores são espaços reservados. Você precisa usar o identificador e a URL de logon real. Contate a [equipe de suporte do ITRP](https://www.itrp.com/support) para obter os valores. Você também pode ver os padrões exibidos na caixa de diálogo **Configuração Básica de SAML** no portal do Azure.

5. Na seção **Certificado de Autenticação SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**:

    ![Ícone Editar](common/edit-certificate.png)

6. Na caixa de diálogo **Certificado de Autenticação SAML**, copie o valor da **Impressão Digital** e salve-o:

    ![Copie o valor da Impressão Digital](common/copy-thumbprint.png)

7. Na seção **Configurar o ITRP**, copie as URLs apropriadas de acordo com suas necessidades:

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    1. **Identificador do Azure AD**.

    1. **URL de Logoff**.

### <a name="configure-itrp-single-sign-on"></a>Configurar logon único do ITRP

1. Em uma nova janela do navegador da Web, entre em seu site de empresa do ITRP como administrador.

1. Na parte superior da janela, selecione o ícone **Configurações**:

    ![Ícone de Configurações](./media/itrp-tutorial/ic775570.png "Ícone de Configurações")

1. No painel à esquerda, selecione **Logon Único**:

    ![Selecionar logon único](./media/itrp-tutorial/ic775571.png "Selecionar logon único")

1. Na seção de configuração de **Logon único**, execute as seguintes etapas.

    ![Seção Logon único](./media/itrp-tutorial/ic775572.png "Seção Logon único")

    ![Seção Logon único](./media/itrp-tutorial/ic775573.png "Seção Logon único")

    1. Selecione **Habilitado**.

    1. Na caixa **URL de logoff remoto**, cole o valor da **URL de logoff** copiado do portal do Azure.

    1. Na caixa **URL de SSO de SAML L**, cole o valor da **URL de logon** copiado do portal do Azure.

    1. Na caixa **Impressão digital do certificado**, cole o valor da **Impressão Digital** do certificado copiado do portal do Azure.

    1. Clique em **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao ITRP.

1. No portal do Azure, selecione **Aplicativos empresariais**, selecione **Todos os aplicativos** e, em seguida, **ITRP**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ITRP**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da janela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da janela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-an-itrp-test-user"></a>Criar um usuário de teste do ITRP

Para permitir que os usuários do Azure AD entrem no ITRP, você precisará adicioná-los ao ITRP. Você precisará adicioná-los manualmente.

Para criar uma conta de usuário, siga estas etapas:

1. Entre em seu locatário do ITRP.

1. Na parte superior da janela, selecione o ícone **Registros**:

    ![Ícone de Registros](./media/itrp-tutorial/ic775575.png "Ícone de Registros")

1. No menu, selecione **Pessoas**:

    ![Selecionar Pessoas](./media/itrp-tutorial/ic775587.png "Selecionar Pessoas")

1. Selecione o sinal de adição ( **+** ) para adicionar uma pessoa:

    ![Selecionar o sinal de adição](./media/itrp-tutorial/ic775576.png "Selecionar o sinal de adição")

1. Na caixa de diálogo **Adicionar Nova Pessoa**, execute as seguintes etapas.

    ![Caixa de diálogo Adicionar Nova Pessoa](./media/itrp-tutorial/ic775577.png "Caixa de diálogo Adicionar Nova Pessoa")

    1. Insira o nome e o endereço de email de uma conta válida do Azure AD que você deseja adicionar.

    1. Clique em **Salvar**.

> [!NOTE]
> Você pode usar qualquer ferramenta de criação de conta de usuário ou API fornecida pelo ITRP para provisionar as contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do ITRP no Painel de Acesso, você será conectado automaticamente à instância do ITRP, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

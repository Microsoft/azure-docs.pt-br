---
title: 'Tutorial: Integração do Azure Active Directory ao Percolate | Microsoft Docs'
description: Neste tutorial você aprenderá a configurar o logon único entre o Azure Active Directory e o Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094609"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Tutorial: Integração do Azure Active Directory ao Percolate

Neste tutorial, você aprenderá a integrar o Percolate ao Azure AD (Azure Active Directory).

Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao Percolate.
* É possível permitir que seus usuários entrem automaticamente no Percolate (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Percolate, você precisa ter:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para logon único do Percolate que tenha o logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Percolate é compatível com SSO iniciado por SP e IdP.

## <a name="add-percolate-from-the-gallery"></a>Adicionar o Percolate por meio da galeria

Para configurar a integração do Percolate ao Azure AD, é necessário adicionar o Percolate da galeria à sua lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**:

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Digite **Percolate** na caixa de pesquisa. Selecione **Percolate** nos resultados da pesquisa e selecione **Adicionar**.

     ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Percolate usando um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no Percolate.

Para configurar e testar o logon único do Azure AD com o Percolate, você precisará concluir estas etapas:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do Percolate](#configure-percolate-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Criar um usuário de teste do Percolate](#create-a-percolate-test-user)** que esteja vinculado à representação do Azure AD do usuário.
6. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Percolate, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Percolate**, selecione **Logon único**:

    ![Selecionar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica de SAML**, não é necessário nenhuma ação para configurar o aplicativo no modo iniciado por IdP. O aplicativo já está integrado com o Azure.

    ![Informações de logon único de URLs e Domínio do Percolate](common/preintegrated.png)

5. Se quiser configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais** e, na caixa **URL de logon**, digite **https://percolate.com/app/login** :

   ![Informações de logon único de URLs e Domínio do Percolate](common/metadata-upload-additional-signon.png)
6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o ícone **Copiar** para a **URL de metadados de federação de aplicativos**. Salve essa URL.

    ![Copiar a URL de metadados de federação de aplicativos](common/copy-metadataurl.png)

7. Na seção **Configurar Percolate**, copie as URLs adequadas, de acordo com suas necessidades.

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    1. **Identificador do Azure AD**.

    1. **URL de Logoff**.

### <a name="configure-percolate-single-sign-on"></a>Configurar logon único do Percolate

1. Em uma nova janela do navegador da Web, entre no Percolate como administrador.

2. No lado esquerdo da Página Inicial, selecione **Configurações**:
    
    ![Escolha Configurações](./media/percolate-tutorial/configure01.png)

3. No painel esquerdo, selecione **SSO** em **Organização**:

    ![Selecionar SSO em Organização](./media/percolate-tutorial/configure02.png)

    1. Na caixa **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    1. Na caixa **ID de Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    1. No Bloco de Notas, abra o certificado codificado em Base 64 que você baixou pelo portal do Azure. Copie seu conteúdo e cole-o na caixa **Certificados x509**.

    1. Na caixa **Atributo de email**, insira **emailaddress**.

    1. A caixa **URL de metadados do provedor de identidade** é um campo opcional. Se você copiou uma **URL de metadados de federação do aplicativo** no portal do Azure, você pode colar nesta caixa.

    1. Na lista **AuthNRequests deve ser assinado?** , selecione **Não**.

    1. Na lista **Habilitar provisionamento automático de SSO**, selecione **Não**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure AD concedendo a ela o acesso ao Percolate.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, selecione **Percolate**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Percolate**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecionar Usuários e grupos](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-percolate-test-user"></a>Criar um usuário de teste do Percolate

Para permitir que os usuários do Azure AD entrem no Percolate, você precisará adicioná-los ao Percolate. Você precisará adicioná-los manualmente.

Para criar uma conta de usuário, siga estas etapas:

1. Entre no Percolate como administrador.

2. No painel esquerdo, selecione **Usuários** em **Organização**. Selecione **Novos usuários**:

    ![Selecionar Novos usuários](./media/percolate-tutorial/configure03.png)

3. Na página **Criar usuários**, realize as seguintes etapas.

    ![Página Criar usuários](./media/percolate-tutorial/configure04.png)

    1. Na caixa **Email**, insira o endereço de email do usuário. Por exemplo, brittasimon@contoso.com.

    1. Na caixa **Nome completo**, digite o nome do usuário. Por exemplo, **Brendafernandes**.

    1. Selecione **Criar usuários**.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Percolate no Painel de Acesso, você deverá entrar automaticamente na instância do Percolate para a qual você configurou o SSO. Para obter mais informações, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
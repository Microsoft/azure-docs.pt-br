---
title: 'Tutorial: Integração do Azure Active Directory ao Sprinklr | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 9e4025d040783bff1cd85fb46d571e3a89967892
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089655"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Integração do Azure Active Directory ao Sprinklr

Neste tutorial, você aprenderá a integrar o Sprinklr ao Azure AD (Azure Active Directory).
A integração do Sprinklr ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Sprinklr.
* É possível permitir que seus usuários entrem automaticamente no Sprinklr (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Sprinklr, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Sprinklr habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Sprinklr é compatível com o SSO iniciado por **SP**

## <a name="adding-sprinklr-from-the-gallery"></a>Adicionar o Sprinklr da galeria

Para configurar a integração do Sprinklr ao Azure AD, você precisará adicionar o Sprinklr da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Sprinklr da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Sprinklr**, selecione **Sprinklr** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Sprinklr na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Sprinklr, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Sprinklr.

Para configurar e testar o logon único do Azure AD com o Sprinklr, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Sprinklr](#configure-sprinklr-single-sign-on)** – para configurar as definições de Logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Sprinklr](#create-sprinklr-test-user)** – para ter um equivalente de Brenda Fernandes no Sprinklr que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Sprinklr, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Sprinklr**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações sobre o logon único do domínio e URLs do Sprinklr](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.sprinklr.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do cliente Sprinklr](https://www.sprinklr.com/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar Sprinklr**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-sprinklr-single-sign-on"></a>Configurar logon único do Sprinklr

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do Sprinklr como um administrador.

1. Vá para **Administração \> Configurações**.

    ![Administração](./media/sprinklr-tutorial/ic782907.png "Administração")

1. Vá para **Gerenciar Parceiro \> Logon Único** no painel à esquerda.

    ![Gerenciar Parceiro](./media/sprinklr-tutorial/ic782908.png "Gerenciar Parceiro")

1. Clique em **+Adicionar Logons Únicos**.

    ![Logons Únicos](./media/sprinklr-tutorial/ic782909.png "Logons Únicos")

1. Na página **Logon Único** , realize as seguintes etapas:

    ![Logons Únicos](./media/sprinklr-tutorial/ic782910.png "Logons Únicos")

    a. Na caixa de texto **Nome**, digite um nome para a sua configuração (por exemplo: *WAADSSOTest*).

    b. Selecione **Habilitado**.

    c. Selecione **Usar novo Certificado de SSO**.

    d. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de Identidade**.

    e. Cole o valor do **Identificador do Azure AD** que você copiou da caixa de texto **ID da entidade**.

    f. Cole o valor da **URL de logon** que você copiou do portal do Azure na caixa de texto **URL de Logon do Provedor de Identidade**.

    g. Cole o valor da **URL de logoff** que você copiou do portal do Azure na caixa de texto **URL de Logon do Provedor de Identidade**.

    h. Para **Tipo de ID de Usuário do SAML**, selecione **A declaração contém o nome de usuário de sprinklr.com do Usuário**.

    i. Para **Local da ID de Usuário do SAML**, selecione **A ID de Usuário está contida no elemento Identificador de Nome da instrução Subject**.

    j. Clique em **Save** (Salvar).

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Sprinklr.

1. No portal do Azure, selecione **Aplicativos empresariais**, selecione **Todos os aplicativos** e, em seguida, selecione **Sprinklr**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Sprinklr**.

    ![O link do Sprinklr na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sprinklr-test-user"></a>Criar usuário de teste do Sprinklr

1. Faça logon em seu site de empresa do Sprinklr como um administrador.

1. Vá para **Administração \> Configurações**.

    ![Administração](./media/sprinklr-tutorial/ic782907.png "Administração")

1. Vá para **Gerenciar Cliente \> Usuários** no painel à esquerda.

    ![Configurações](./media/sprinklr-tutorial/ic782914.png "Configurações")

1. Clique em **Adicionar Usuário**.

    ![Configurações](./media/sprinklr-tutorial/ic782915.png "Configurações")

1. No diálogo **Editar usuário** , realize as seguintes etapas:

    ![Editar usuário](./media/sprinklr-tutorial/ic782916.png "Editar usuário")

    a. Nas caixas de texto **Email**, **Nome** e **Sobrenome**, digite as informações de uma conta de usuário do Azure AD que você deseja provisionar.

    b. Selecione **Senha Desabilitada**.

    c. Selecione **Idioma**.

    d. Selecione **Tipo de Usuário**.

    e. Clique em **Atualizar**.

    > [!IMPORTANT]
    > **Senha Desabilitada** deve ser marcada para permitir que um usuário faça logon por meio de um Provedor de identidade. 

1. Vá para **Função**e realize as seguintes etapas:

    ![Funções de Parceiro](./media/sprinklr-tutorial/ic782917.png "Funções de Parceiro")

    a. Na lista **Global**, selecione **ALL_Permissions**.  

    b. Clique em **Atualizar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do Sprinklr ou APIs fornecidas pelo Sprinklr para provisionar contas de usuário do AD do Azure.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Sprinklr no Painel de Acesso, você deverá entrar automaticamente no Sprinklr no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Tutorial: Integração do Azure Active Directory ao Vidyard | Microsoft Docs'
description: Aprenda como configurar logon único entre o Azure Active Directory e o Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: a55ec7afc94440ea8b6a48ed1507476d362df6c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087435"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Tutorial: Integração do Azure Active Directory ao Vidyard

Neste tutorial, você aprenderá como integrar o Vidyard com Azure AD (Azure Active Directory).
A integração do Vidyard com Azure AD fornece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Vidyard.
* Você pode permitir que os usuários sejam conectados automaticamente ao Vidyard (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Vidyard, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Vidyard

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Vidyard é compatível com SSO iniciado por **SP** e **IDP**

* O Vidyard dá suporte ao provisionamento de usuário **Just In Time**

## <a name="adding-vidyard-from-the-gallery"></a>Adicionar Vidyard da galeria

Para configurar a integração do Vidyard ao Azure AD, você precisa adicionar o Vidyard da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Vidyard da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Vidyard**, selecione **Vidyard** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

     ![Vidyard na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Vidyard com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Vidyard.

Para configurar e testar o logon único do Azure AD com Vidyard, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Vidyard](#configure-vidyard-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Vidyard](#create-vidyard-test-user)** – para ter um equivalente de Brenda Fernandes no Vidyard vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Vidyard, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Vidyard**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Voyance](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Voyance](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Esses valores não são reais. Você atualizará esses valores com o identificador, URL de resposta e URL de logon reais, explicado mais adiante no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Vidyard**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-vidyard-single-sign-on"></a>Configurar o logon único do Vidyard

1. Em uma janela diferente do navegador da Web, entre no site da sua empresa do Vidyard Software como administrador.

2. No painel do Vidyard, selecione **Grupo** > **Segurança**

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure1.png)

3. Clique na guia **Novo Perfil**.

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure2.png)

4. Na seção **Configuração SAML**, execute as seguintes etapas:

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Insira o nome do perfil geral na caixa de texto **Nome do Perfil**.

    b. Copie o valor da **Página de Logon do Usuário de SSO** e cole-o na caixa de texto **URL de Entrada** na seção **Configuração Básica de SAML** no portal do Azure.

    c. Copie o valor **URL de ACS** e cole-o na caixa de texto **URL de resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    d. Copie o valor de **Emissor/URL de Metadados** e cole-o na caixa de texto **Identificador** na seção **Configuração Básica de SAML** no portal do Azure.

    e. Abra o arquivo de certificado baixado do portal do Azure no Bloco de Notas e cole-o na caixa de texto **Certificado X.509**.

    f. Na caixa de texto **URL do Ponto de extremidade SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    g. Clique em **Confirmar**.

5. Na guia Logon Único, selecione **Atribuir** ao lado de um perfil existente

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Depois de criar um perfil de SSO, atribua-o a qualquer grupo para o qual os usuários precisarão de acesso através do Azure. Se o usuário não existir dentro do grupo ao qual ele foi atribuído, o Vidyard criará automaticamente uma conta de usuário e atribuirá a função em tempo real.

6. Selecione o grupo da organização, que fica visível nos **Grupos Disponíveis para Atribuir**.

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure5.png)

7. É possível visualizar os grupos atribuídos em **Grupos Atualmente Atribuídos**. Selecione uma função para o grupo de acordo com a organização e clique em **Confirmar**.

    ![Configuração do Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Para saber mais, veja [este documento](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Vidyard.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Vidyard**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Vidyard**.

    ![O link do Vidyard na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-vidyard-test-user"></a>Crie usuário de teste do Vidyard

Nesta seção, um usuário chamado Brenda Fernandes será criado no Vidyard. O Vidyard dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Vidyard, um novo será criado após a autenticação.

>[!Note]
>Se for necessário criar um usuário manualmente, contate a [equipe de suporte do Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Vidyard no Painel de Acesso, você deverá ser conectado automaticamente ao Vidyard no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Tutorial: Integração do Azure Active Directory ao Trisotech Digital Enterprise Server | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Trisotech Digital Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 56399f99ede611c4a120603cce3a3eede2728c6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088256"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Tutorial: Integração do Azure Active Directory ao Trisotech Digital Enterprise Server

Neste tutorial, você aprenderá a integrar o Trisotech Digital Enterprise Server com o Microsoft Azure AD (Azure Active Directory).
A integração do Trisotech Digital Enterprise Server com o Microsoft Azure AD oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure AD quem tem acesso ao Trisotech Digital Enterprise Server.
* Você pode habilitar os usuários para serem automaticamente conectados ao Trisotech Digital Enterprise Server (Logon Único) com as respectivas contas do Microsoft Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com o Trisotech Digital Enterprise Server, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada de logon único do Trisotech Digital Enterprise Server

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Trisotech Digital Enterprise Server é compatível com SSO iniciado por **SP**

* O Trisotech Digital Enterprise Server é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Adicionar o Trisotech Digital Enterprise Server da galeria

Para configurar a integração do Trisotech Digital Enterprise Server ao Microsoft Azure AD, você precisa adicionar o Trisotech Digital Enterprise Server da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Trisotech Digital Enterprise Server por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Trisotech Digital Enterprise Server**, selecione **Trisotech Digital Enterprise Server** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Trisotech Digital Enterprise Server na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Trisotech Digital Enterprise Server, com base em um usuário de teste chamado **Brenda Fernandes**.
Para logon único funcionar, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure AD e o usuário relacionado do Trisotech Digital Enterprise Server.

Para configurar e testar o logon único do Microsoft Azure AD com o Trisotech Digital Enterprise Server, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Trisotech Digital Enterprise Server](#configure-trisotech-digital-enterprise-server-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Trisotech Digital Enterprise Server](#create-trisotech-digital-enterprise-server-test-user)** : para ter um equivalente de Brenda Fernandes no Trisotech Digital Enterprise Server que esteja vinculado à representação do usuário no Microsoft Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure AD com o Trisotech Digital Enterprise Server, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Trisotech Digital Enterprise Server**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Trisotech Digital Enterprise Server](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.trisotech.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Trisotech Digital Enterprise Server](mailto:support@trisotech.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Configurar logon único do Trisotech Digital Enterprise Server

1. Em uma janela diferente do navegador da Web, entre no site da empresa na configuração do Trisotech Digital Enterprise Server como um administrador.

2. Clique no **Menu ícone** e, em seguida, selecione **Administração**.

    ![Configurar o logon único](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Selecione **Provedor de Usuário**.

    ![Configurar o logon único](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. Na seção**Configurações do Provedor de Usuário**, execute as seguintes etapas:

    ![Configurar o logon único](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Selecione **SAML 2 (Secured Assertion Markup Language 2)** da lista suspensa no **Método de Autenticação**.

    b. Na caixa de texto **URL de Metadados**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure.

    c. Na caixa de texto da **ID do Aplicativo**, insira a URL usando o padrão a seguir: `https://<companyname>.trisotech.com`.

    d. Clique em **Salvar**

    e. Inserir o nome de domínio na caixa de texto **Domínios Permitidos (vazio significa todos)** , ele atribui automaticamente licenças para usuários que correspondem aos domínios permitidos

    f. Clique em **Salvar**

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Trisotech Digital Enterprise Server.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **Trisotech Digital Enterprise Server**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Trisotech Digital Enterprise Server**.

    ![O link do Trisotech Digital Enterprise Server na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Criar usuário de teste do Trisotech Digital Enterprise Server

Nesta seção, um usuário chamado Brenda Fernandes é criado no Trisotech Digital Enterprise Server. O Trisotech Digital Enterprise Server é compatível com provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no Trisotech Digital Enterprise Server, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Trisotech Digital Enterprise Server no Painel de Acesso, você deverá entrar automaticamente no aplicativo Trisotech Digital Enterprise Server para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


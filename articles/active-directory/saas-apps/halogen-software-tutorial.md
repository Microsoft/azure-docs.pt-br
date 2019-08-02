---
title: 'Tutorial: Integração do Azure Active Directory com o Halogen Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Halogen Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8e1f4f0f2bd3521a312523fa9e36dcf14492862
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101385"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Tutorial: Integração do Azure Active Directory com o Halogen Software

Neste tutorial, você aprenderá a integrar o Halogen Software ao Azure AD (Azure Active Directory).
A integração do Halogen Software ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Halogen Software.
* Você pode permitir que os usuários sejam conectados automaticamente ao Halogen Software (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Halogen Software, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Halogen Software

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Halogen Software dá suporte ao SSO iniciado por **SP**

## <a name="adding-halogen-software-from-the-gallery"></a>Adicionar o Halogen Software por meio da galeria

Para configurar a integração do Halogen Software com o Azure AD, você precisa adicionar o Halogen Software, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Halogen Software por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Halogen Software**, selecione **Halogen Software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Halogen Software na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Halogen Software, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Halogen Software.

Para configurar e testar o logon único do Azure AD com o Halogen Software, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Halogen Software](#configure-halogen-software-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Halogen Software](#create-halogen-software-test-user)** – para ter um equivalente de Brenda Fernandes no Halogen Software que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Halogen Software, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Halogen Software**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Halogen Software](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://global.hgncloud.com/<companyname>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://global.halogensoftware.com/<companyname>`|
    | `https://global.hgncloud.com/<companyname>`|
    | |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao Cliente do Halogen Software](https://support.halogensoftware.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Halogen Software**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-halogen-software-single-sign-on"></a>Configurar o logon único do Halogen Software

1. Em uma janela diferente do navegador, faça logon no aplicativo **Halogen Software** como administrador.

2. Clique na guia **Opções** .
  
    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_12.png)

3. No painel de navegação esquerdo, clique em **Configuração do SAML**.
  
    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_13.png)

4. Na página **Configuração do SAML** , realize as seguintes etapas:

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_14.png)

    a. Como **Identificador exclusivo**, selecione **NameID**.

    b. Em **Identificador exclusivo mapeia para**, selecione **Nome de usuário**.
  
    c. Para carregar o arquivo de metadados baixado, clique em **Procurar** para selecionar o arquivo e clique em **Carregar arquivo**.

    d. Para testar a configuração, clique em **Executar Teste**.

    > [!NOTE]
    > Você precisa esperar pela mensagem "*O teste de SAML foi concluído. Feche esta janela*". Feche a janela do navegador aberta. A caixa de seleção **Habilitar SAML** só será habilitada se o teste for concluído.

    e. Selecione **Habilitar SAML**.

    f. Clique em **Salvar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilita Brenda Fernandes a usar o logon único do Azure concedendo acesso ao Halogen Software.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Halogen Software**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Halogen Software**.

    ![O link do Halogen Software na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-halogen-software-test-user"></a>Criar um usuário de teste do Halogen Software

O objetivo desta seção é criar um usuário chamado Britta Simon no Halogen Software.

**Para criar um usuário chamado Britta Simon no Halogen Software, execute as seguintes etapas:**

1. Faça logon no aplicativo **Halogen Software** como administrador.

2. Clique na guia **Central do Usuário** e clique em **Criar Usuário**.

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_300.png)  

3. Na página do diálogo **Novo Usuário** , realize as seguintes etapas:

    ![O que é o Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_301.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **Fernandes**.

    c. Na caixa de texto **Nome de Usuário**, digite **Brenda Fernandes**, o nome de usuário como no portal do Azure.

    d. Na caixa de texto **Senha** , digite uma senha para Britta.

    e. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Halogen Software no Painel de Acesso, você deverá ser conectado automaticamente ao Halogen Software, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
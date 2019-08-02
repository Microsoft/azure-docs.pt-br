---
title: 'Tutorial: Integração do Azure Active Directory com o UserVoice | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: c0c259d3d05232aa70016771e2a2bce7622730a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087630"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integração do Azure Active Directory com o UserVoice

Neste tutorial, você aprenderá a integrar o UserVoice ao Azure AD (Azure Active Directory).
A integração do UserVoice ao Azure AD oferece os seguintes benefícios:

* No AD do Azure, é possível controlar quem tem acesso ao UserVoice.
* Você pode permitir que seus usuários entrem automaticamente no UserVoice (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao UserVoice, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do UserVoice com logon único habilitado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O UserVoice dá suporte ao SSO iniciado por **SP**

## <a name="adding-uservoice-from-the-gallery"></a>Adicionar UserVoice pela galeria

Para configurar a integração do UserVoice com o Azure AD, você precisará adicionar o UserVoice à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o UserVoice pela galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **UserVoice**, selecione **UserVoice** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![UserVoice na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o UserVoice, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do UserVoice.

Para configurar e testar o logon único do AD do Azure com o UserVoice, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do UserVoice](#configure-uservoice-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do UserVoice](#create-uservoice-test-user)** – para ter um equivalente de Brenda Fernandes no UserVoice que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o UserVoice, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **UserVoice**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de URLs e Domínio do UserVoice](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenantname>.UserVoice.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do cliente do UserVoice](https://www.uservoice.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

6. Na seção **Certificado de Autenticação SAML**, copie a **Impressão Digital** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

7. Na seção **Configurar o UserVoice**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-uservoice-single-sign-on"></a>Configurar o logon único do UserVoice

1. Em uma janela diferente do navegador da Web, entre no site corporativo do UserVoice como administrador.

2. Na barra de ferramentas na parte superior, clique em **Configurações** e, em seguida, selecione o **portal da Web** no menu.
   
    ![Seção Configurações no lado do aplicativo](./media/uservoice-tutorial/ic777519.png "Configurações")

3. Na guia **Portal da Web**, na seção **Autenticação de usuário**, clique em **Editar** para abrir a página do diálogo **Editar Autenticação de Usuário**.
   
    ![Guia Portal da Web](./media/uservoice-tutorial/ic777520.png "Portal da Web")

4. Na página do diálogo **Editar Autenticação de Usuário** , realize as seguintes etapas:
   
    ![Editar autenticação de usuário](./media/uservoice-tutorial/ic777521.png "Editar autenticação de usuário")
   
    a. Clique em **SSO (Logon Único)** .
 
    b. Cole o valor da **URL de Logon** copiado do portal do Azure para a caixa de texto **Entrada Remota de SSO**.

    c. Cole o valor da **URL de Logoff** copiado do portal do Azure para a **caixa de texto Saída Remota de SSO**.
 
    d. Cole o valor da **Impressão digital** copiado do portal do Azure na caixa de texto **Impressão digital de certificado SHA1 atual**.
    
    e. Clique em **Salvar Configurações de Autenticação**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao UserVoice.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **UserVoice**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **UserVoice**.

    ![O link do UserVoice na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-uservoice-test-user"></a>Criar usuário de teste do UserVoice

Para permitir que os usuários do Azure AD entrem no UserVoice, eles devem ser provisionados no UserVoice. No caso do UserVoice, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Entre no seu locatário **UserVoice**.

2. Vá para **Configurações**.
   
    ![Configurações](./media/uservoice-tutorial/ic777811.png "Configurações")

3. Clique em **Geral**.

4. Clique em **Agentes e permissões**.
   
    ![Agentes e permissões](./media/uservoice-tutorial/ic777812.png "Agentes e permissões")

5. Clique em **Adicionar administradores**.
   
    ![Adicionar administradores](./media/uservoice-tutorial/ic777813.png "Adicionar administradores")

6. No diálogo **Convidar administradores** , realize as seguintes etapas:
   
    ![Convidar administradores](./media/uservoice-tutorial/ic777814.png "Convidar administradores")
   
    a. Na caixa de texto Emails, digite o endereço de email da conta que você deseja provisionar e clique em **Adicionar**.
   
    b. Clique em **Convidar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário do UserVoice ou APIs fornecidas pelo UserVoice para provisionar contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do UserVoice no Painel de Acesso, você deverá ser conectado automaticamente ao UserVoice no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


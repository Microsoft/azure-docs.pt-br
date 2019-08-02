---
title: 'Tutorial: Integração do Azure Active Directory ao Freshservice | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fbf69ba814b99434e933ed700fb0c8c842c3312
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101849"
---
# <a name="tutorial-integrate-freshservice-with-azure-active-directory"></a>Tutorial: Integrar o Freshservice ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Freshservice ao Azure AD (Azure Active Directory). Quando integrar o Freshservice ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Freshservice.
* Permitir que os usuários sejam conectados automaticamente ao Freshservice com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para logon único (SSO) do Freshservice.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Freshservice dá suporte ao SSO iniciado por **SP**

## <a name="adding-freshservice-from-the-gallery"></a>Adicionando Freshservice da galeria

Para configurar a integração do Freshservice ao Azure AD, você precisa adicionar o Freshservice da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Freshservice** na caixa de pesquisa.
1. Escolha **Freshservice** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Freshservice usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Freshservice.

Para configurar e testar o SSO do Azure AD com o Freshservice, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Freshservice](#configure-freshservice-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Freshservice](#create-freshservice-test-user)** – para ter um equivalente de Brenda Fernandes no Freshservice que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Freshservice**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<democompany>.freshservice.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com [equipe de suporte ao Cliente do Freshservice](https://support.freshservice.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie a **Impressão Digital** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o Freshservice**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-freshservice-sso"></a>Configurar o SSO do Freshservice

1. Para automatizar a configuração no Freshservice, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Configurar o Freshservice** direcionará você ao aplicativo Freshservice. Nele, forneça as credenciais de administrador para entrar no Freshservice. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Freshservice manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Freshservice como administrador e execute as seguintes etapas:

4. No menu na parte superior, clique em **Administrador**.

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

5. No **Portal do Cliente**, clique em **Segurança**.

    ![Segurança](./media/freshservice-tutorial/ic790815.png "Segurança")

6. Na seção **Segurança** , realize as seguintes etapas:

    ![Logon Único](./media/freshservice-tutorial/ic790816.png "Logon Único")

    a. Ative o **Logon Único**.

    b. Selecione **SSO do SAML**.

    c. Na caixa de texto **URL de Logon do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    e. Na caixa de texto **Impressão Digital do Certificado de Segurança**, cole o valor do certificado de **IMPRESSÃO DIGITAL**, copiado do portal do Azure.

    f. Clique em **Salvar**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela o acesso ao Freshservice.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Freshservice**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-freshservice-test-user"></a>Criar um usuário de teste do Freshservice

Para permitir que os usuários do Azure AD façam logon no FreshService, eles devem ser provisionados no Freshservice. No caso do FreshService, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site da empresa **FreshService** como administrador.

2. No menu na parte superior, clique em **Administrador**.

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

3. Na seção **Gerenciamento de Usuários**, clique em **Solicitantes**.

    ![Solicitantes](./media/freshservice-tutorial/ic790818.png "Solicitantes")

4. Clique em **Novo Solicitante**.

    ![Novos Solicitantes](./media/freshservice-tutorial/ic790819.png "Novos Solicitantes")

5. Na seção **Novo Solicitante** , realize as seguintes etapas:

    ![Novo Solicitante](./media/freshservice-tutorial/ic790820.png "Novo Solicitante")  

    a. Insira os atributos **Nome** e **Email** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

    b. Clique em **Save** (Salvar).

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email que inclui um link para confirmar a conta antes que ela se torne ativa
    >  

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do FreshService ou APIs fornecidas pelo FreshService para provisionar as contas de usuário do AAD.

### <a name="test-sso"></a>Testar o SSO

Quando escolher o bloco do Freshservice no Painel de Acesso, você deverá ser conectado automaticamente ao Freshservice, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

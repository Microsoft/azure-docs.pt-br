---
title: 'Tutorial: Integração do Azure Active Directory ao ExpenseIn | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ExpenseIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09542013dff3a18965d1070216a938c26a144e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102854"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Tutorial: Integrar o ExpenseIn ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o ExpenseIn ao Azure AD (Azure Active Directory). Quando integrar o ExpenseIn ao Azure AD, será possível:

* Controlar no Azure AD quem tem acesso ao ExpenseIn.
* Permitir que seus usuários entrem automaticamente no ExpenseIn com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do ExpenseIn.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O ExpenseIn é compatível com o SSO iniciado por **SP e IDP**.

## <a name="adding-expensein-from-the-gallery"></a>Como adicionar o ExpenseIn por meio da galeria

Para configurar a integração do ExpenseIn ao Azure AD, é necessário adicionar o ExpenseIn por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ExpenseIn** na caixa de pesquisa.
1. Selecione **ExpenseIn** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o ExpenseIn usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ExpenseIn.

Para configurar e testar o SSO do Azure AD com o ExpenseIn, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configure o ExpenseIn](#configure-expensein)** para definir as configurações de SSO no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B. Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do ExpenseIn](#create-expensein-test-user)** para ter um equivalente de B. Fernandes no ExpenseIn que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ExpenseIn**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute a seguinte etapa:

    Na caixa de texto **URL de Resposta**, digite qualquer uma das URLs:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.expensein.com/saml`

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de Metadados de Federação do Aplicativo** e clique em **Baixar** para baixar o **Certificado (Base64)** e salve-o em seu computador.

   ![O link de download do Certificado](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Na seção **Configurar o ExpenseIn**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Configurar o ExpenseIn

1. Para automatizar a configuração no ExpenseIn, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Instalação do ExpenseIn**. Você será direcionado ao aplicativo ExpenseIn. Em seguida, forneça as credenciais de administrador para entrar no ExpenseIn. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o ExpenseIn manualmente, abra uma nova janela do navegador da Web, entre no site da empresa ExpenseIn como administrador e execute as seguintes etapas:

4. Clique em **Administrador** na parte superior da página e, em seguida, navegue até **Logon único** e clique em **Adicionar provedor**.

     ![Configuração do ExpenseIn](./media/expenseIn-tutorial/config01.png)

5. No item pop-up **Novo Provedor de Identidade**, execute as seguintes etapas:

    ![Configuração do ExpenseIn](./media/expenseIn-tutorial/config02.png)

    a. Na caixa de texto **Nome do Provedor**, digite o nome como Azure.

    b. Selecione **Sim** para **Permitir fazer logon no provedor iniciado**.

    c. Na caixa de texto **URL de Destino**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa de texto **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    e. Abra o Certificado (Base64) no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado**.

    f. Clique em **Criar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao ExpenseIn.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ExpenseIn**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B. Fernandes** na lista Usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-expensein-test-user"></a>Criar usuário de teste do ExpenseIn

Para permitir que os usuários do Azure AD entrem no ExpenseIn, eles precisarão ser provisionados no ExpenseIn. No ExpenseIn, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no ExpenseIn como Administrador.

2. Clique em **Administrador** na parte superior da página e, em seguida, navegue até **Usuários** e clique em **Novo Usuário**.

     ![Configuração do ExpenseIn](./media/expenseIn-tutorial/config03.png)

3. No item pop-up **Detalhes**, execute as seguintes etapas:

    ![Configuração do ExpenseIn](./media/expenseIn-tutorial/config04.png)

    a. Na caixa de texto **Nome**, insira o nome do usuário como **B**.

    b. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

    c. Na caixa de texto **Email**, insira o email do usuário como `B.Simon@contoso.com`.

    d. Clique em **Criar**.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do ExpenseIn no Painel de Acesso, você deverá entrar automaticamente no ExpenseIn para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

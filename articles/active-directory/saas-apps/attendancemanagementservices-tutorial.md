---
title: 'Tutorial: Integração do Azure Active Directory com Serviços de Gerenciamento de Participação | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e os Serviços de Gerenciamento de Participação.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7143d0afce7a3644286703a9eba0da1ee45305f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106554"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Tutorial: Integração do Azure Active Directory com Serviços de Gerenciamento de Participação

Neste tutorial, você aprenderá a integrar os Serviços de Gerenciamento de Participação ao Microsoft Azure AD (Active Directory).
A integração dos Serviços de Gerenciamento de Participação ao Microsoft Azure AD oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure AD quem tem acesso aos Serviços de Gerenciamento de Participação.
* Você pode permitir que os usuários façam logon automaticamente nos Serviços de Gerenciamento de Participação (Logon Único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com os Serviços de Gerenciamento de Participação, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura dos Serviços de Gerenciamento de Participação habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Os Serviços de Gerenciamento de Participação são compatíveis com SSO iniciado por **SP**

## <a name="adding-attendance-management-services-from-the-gallery"></a>Adicionar Serviços de Gerenciamento de Participação da galeria

Para configurar a integração dos Serviços de Gerenciamento de Participação com o Microsoft Azure AD, você precisa adicionar os Serviços de Gerenciamento de Participação, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar os Serviços de Gerenciamento de Participação da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Serviços de Gerenciamento de Participação**, selecione **Serviços de Gerenciamento de Participação** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Serviços de Gerenciamento de Participação na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com os Serviços de Gerenciamento de Participação, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, uma relação de link entre um usuário do Microsoft Azure AD e o usuário relacionado nos Serviços de Gerenciamento de Participação deve ser estabelecida.

Para configurar e testar o logon único do Microsoft Azure AD com os Serviços de Gerenciamento de Participação, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único dos Serviços de Gerenciamento de Participação](#configure-attendance-management-services-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste dos Serviços de Gerenciamento de Participação](#create-attendance-management-services-test-user)** – para ter um equivalente de Brenda Fernandes nos Serviços de Gerenciamento de Participação que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure AD com os Serviços de Gerenciamento de Participação, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Serviços de Gerenciamento de Participação**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e URLs dos Serviços de Gerenciamento de Participação](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do cliente de Serviços de Gerenciamento de Participação](https://www.obcnet.jp/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar Serviços de Gerenciamento de Participação**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-attendance-management-services-single-sign-on"></a>Configurar o logon único dos Serviços de Gerenciamento de Participação

1. Em uma janela de navegador diferente, entre no site de sua empresa dos Serviços de Gerenciamento de Participação como administrador.

1. Clique em **Autenticação SAML** na **seção Gerenciamento de segurança**.

    ![Configuração de Serviços de Gerenciamento de Participação](./media/attendancemanagementservices-tutorial/user1.png)

1. Execute as seguintes etapas:

    ![Configuração de Serviços de Gerenciamento de Participação](./media/attendancemanagementservices-tutorial/user2.png)

    a. Selecione **Usar autenticação SAML**.

    b. Na caixa de texto **Identificador**, cole o valor de **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de ponto de extremidade**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Clique em **Selecionar arquivo** para carregar o certificado que você baixou do Microsoft Azure AD.

    e. Selecione **Desabilitar autenticação por senha**.

    f. Clique em **Registro**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso aos Serviços de Gerenciamento de Participação.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Serviços de Gerenciamento de Participação**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Serviços de Gerenciamento de Participação**.

    ![O link de Serviços de Gerenciamento de Participação na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-attendance-management-services-test-user"></a>Criar um usuário de teste de Serviços de Gerenciamento de Participação

Para permitir que os usuários do Microsoft Azure AD entrem nos Serviços de Gerenciamento de Participação, eles devem ser provisionados nos Serviços de Gerenciamento de Participação. No caso de Serviços de Gerenciamento de Participação, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site de sua empresa dos Serviços de Gerenciamento de Participação como administrador.

1. Clique em **Gerenciamento de usuários** na **seção Gerenciamento de segurança**.

    ![Adicionar Funcionário](./media/attendancemanagementservices-tutorial/user5.png)

1. Clique em **Novas regras de logon**.

    ![Adicionar Funcionário](./media/attendancemanagementservices-tutorial/user3.png)

1. Na seção **Informações de OBCiD**, realize as seguintes etapas:

    ![Adicionar Funcionário](./media/attendancemanagementservices-tutorial/user4.png)

    a. Na caixa de texto **OBCiD**, digite o email do usuário como `BrittaSimon\@contoso.com`.

    b. Na caixa de texto **Senha**, digite a senha do usuário.

    c. Clique em **Registro**

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Serviços de Gerenciamento de Participação no Painel de Acesso, você deve entrar automaticamente no aplicativo de Serviços de Gerenciamento de Participação para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
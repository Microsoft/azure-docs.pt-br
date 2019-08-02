---
title: 'Tutorial: integração do Azure Active Directory com o ScreenSteps | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 864a9243a9f737506fd4d8cbc3940d7a86711f20
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091657"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: integração do Azure Active Directory com o ScreenSteps

Neste tutorial, você aprende a integrar o ScreenSteps ao Azure AD (Azure Active Directory).
A integração do ScreenSteps ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao ScreenSteps.
* Você pode permitir que seus usuários entrem automaticamente no ScreenSteps (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ScreenSteps, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do ScreenSteps habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ScreenSteps é compatível com o SSO iniciado por **SP**

## <a name="adding-screensteps-from-the-gallery"></a>Adicionar o ScreenSteps por meio da galeria

Para configurar a integração do ScreenSteps ao Azure AD, é necessário adicionar o ScreenSteps à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o ScreenSteps por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ScreenSteps**, selecione **ScreenSteps** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![ScreenSteps na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o ScreenSteps, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ScreenSteps.

Para configurar e testar o logon único do Azure AD com o ScreenSteps, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do ScreenSteps](#configure-screensteps-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do ScreenSteps](#create-screensteps-test-user)** – para ter um equivalente de Brenda Fernandes no ScreenSteps vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ScreenSteps, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ScreenSteps**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ScreenSteps](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com a URL de logon real, o que é explicado posteriormente neste tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar ScreenSteps**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-screensteps-single-sign-on"></a>Configurar o logon único do ScreenSteps

1. Em outra janela do navegador da Web, faça logon em seu site de empresa ScreenSteps como um administrador.

1. Clique em **Configurações da Conta**.

    ![Gerenciamento de contas](./media/screensteps-tutorial/ic778523.png "Gerenciamento de contas")

1. Clique em **Logon Único**.

    ![Autenticação Remota](./media/screensteps-tutorial/ic778524.png "Autenticação Remota")

1. Clique em  **Criar Ponto de Extremidade de Logon Único**.

    ![Autenticação Remota](./media/screensteps-tutorial/ic778525.png "Autenticação Remota")

1. Na seção **Criar um Ponto de Extremidade de Logon Único**, realize as seguintes etapas:

    ![Criar um Ponto de Extremidade de Autenticação](./media/screensteps-tutorial/ic778526.png "Criar um Ponto de Extremidade de Autenticação")

    a. Na caixa de texto **Título** , digite um título.

    b. Na lista **Modo**, selecione **SAML**.

    c. Clique em **Criar**.

1. **Edite** o novo ponto de extremidade.

    ![Editar ponto de extremidade](./media/screensteps-tutorial/ic778528.png "Editar ponto de extremidade")

1. Na seção **Editar um Ponto de Extremidade de Logon Único**, realize as seguintes etapas:

    ![Ponto de Extremidade de Autenticação Remota](./media/screensteps-tutorial/ic778527.png "Ponto de Extremidade de Autenticação Remota")

    a. Clique em **Carregar novo arquivo de Certificado SAML** e carregue o certificado que você baixou do Portal do Azure.

    b. Cole o valor da **URL de Logon**, que você copiou do portal do Azure, na caixa de texto **URL de Logon Remoto**.

    c. Cole o valor da **URL de Logoff**, que você copiou do portal do Azure, na caixa de texto **URL de Logoff**.

    d. Selecione um **Grupo** para o qual atribuir usuários quando eles são provisionados.

    e. Clique em **Atualizar**.

    f. Copie a **URL do Consumidor de SAML** para a área de transferência e cole na caixa de texto **URL de Logon** na seção **Configuração Básica de SAML** no portal do Azure.

    g. Retorne para **Editar Ponto de Extremidade de Logon Único**.

    h. Clique no botão **Tornar padrão para a conta** para usar esse ponto de extremidade para todos os usuários que fizerem logon no ScreenSteps. Como alternativa, você pode clicar no botão **Adicionar ao Site** usar esse ponto de extremidade para sites específicos em **ScreenSteps**.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao ScreenSteps.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **ScreenSteps**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **ScreenSteps**.

    ![O link do ScreenSteps na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-screensteps-test-user"></a>Criar usuário de teste do ScreenSteps

Nesta seção, você criará um usuário chamado Brenda Fernandes no ScreenSteps. Trabalhe com a  [Equipe de suporte ao cliente do ScreenSteps](https://www.screensteps.com/contact)  para adicionar usuários na plataforma do ScreenSteps. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ScreenSteps no Painel de Acesso, você deverá ser conectado automaticamente ao ScreenSteps, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
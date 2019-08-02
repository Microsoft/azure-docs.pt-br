---
title: 'Tutorial: Integração do Azure Active Directory ao Teamphoria | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 05387d2fd9b38ffa8137668e01b6d7cff3346ac8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089005"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Tutorial: Integração do Azure Active Directory ao Teamphoria

Neste tutorial, você aprenderá a integrar o Teamphoria ao Azure AD (Azure Active Directory).
A integração do Teamphoria com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Teamphoria.
* É possível permitir que seus usuários entrem automaticamente no Teamphoria (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Teamphoria, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Teamphoria

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Teamphoria é compatível com o SSO iniciado por **SP**

## <a name="adding-teamphoria-from-the-gallery"></a>Adicionar o Teamphoria da galeria

Para configurar a integração do Teamphoria com o Azure AD, você precisará adicionar o Teamphoria da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Teamphoria da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Teamphoria**, selecione **Teamphoria** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Teamphoria na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Teamphoria baseado em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Teamphoria.

Para configurar e testar o logon único do Azure AD com o Teamphoria, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único do Teamphoria](#configure-teamphoria-single-sign-on)** – para configurar o logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Teamphoria](#create-teamphoria-test-user)** – para ter um equivalente de Brenda Fernandes no Teamphoria que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Teamphoria, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Teamphoria**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Teamphoria](common/sp-intiated.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > O valor da URL de logon não é real. Você precisa atualizar esse valor com a URL de Entrada real. Entre em contato com a [Equipe de suporte do cliente do Teamphoria](https://www.teamphoria.com/) para obter a URL de Entrada. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Teamphoria**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-teamphoria-single-sign-on"></a>Configurar logon único do Teamphoria

1. Para configurar o logon único pelo **Teamphoria**, faça logon em seu aplicativo Teamphoria como administrador.

1. Vá até a opção **CONFIGURAÇÕES DE ADMINISTRAÇÃO** na barra de ferramentas à esquerda e, na guia Configurar, clique em **LOGON ÚNICO** para abrir a janela de configuração de SSO.

    ![Configurar o logon único](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Clique na opção **ADICIONAR NOVO PROVEDOR DE IDENTIDADE** no canto superior direito para abrir o formulário para adicionar as configurações de SSO.

    ![Configurar o logon único](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Insira os detalhes nos campos conforme descrito abaixo:

    ![Configurar o logon único](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOME DE EXIBIÇÃO**: insira o nome de exibição do plug-in na página de administração.

    b. **NOME DO BOTÃO**: o nome da guia que será exibida na página de logon para entrar usando SSO.

    c. **CERTIFICADO**: abra o certificado baixado anteriormente do portal do Azure no bloco de notas, copie o conteúdo e cole-o nesta caixa.

    d. **PONTO DE ENTRADA**: Cole a **URL de logon** copiada anteriormente do portal do Azure.

    e. Alterne a opção para **LIGADO** e clique em **SALVAR**.

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

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo a ela acesso ao Teamphoria.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Teamphoria**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Teamphoria**.

    ![O link do Teamphoria na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-teamphoria-test-user"></a>Criar usuário de teste do Teamphoria

Para permitir que usuários do Azure AD entrem no Teamphoria, eles deverão ser provisionados no Teamphoria. No caso do Teamphoria, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do Teamphoria como administrador.

1. Clique nas configurações de **ADMIN** na barra de ferramentas à esquerda e, na guia **GERENCIAR**, clique em **USUÁRIOS** para abrir a página de administração para usuários.

    ![Adicionar Funcionário](./media/teamphoria-tutorial/admin_manage_users.png)

1. Clique na opção **CONVITE MANUAL**.

    ![Convidar Pessoas](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Nessa página, realize a ação a seguir.

    ![Convidar Pessoas](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Na caixa de texto **ENDEREÇO DE EMAIL**, insira o **endereço de email** do usuário como BrendaFernandes.

    b. Na caixa de texto **NOME**, digite o nome do usuário como **Brenda**.

    c. Na caixa de texto **SOBRENOME**, digite o sobrenome do usuário como **Fernandes**.

    d. Clique em **CONVIDAR 1 USUÁRIO**. O usuário deve aceitar o convite para ser criado no sistema.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Teamphoria no Painel de Acesso, você deverá entrar automaticamente no Teamphoria para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


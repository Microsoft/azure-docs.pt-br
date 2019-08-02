---
title: 'Tutorial: Integração do Azure Active Directory ao HRworks Single Sign-On | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HRworks Single Sign-On.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 790df60f973e6f86bd4424173909159fdd81ee0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100851"
---
# <a name="tutorial-azure-active-directory-integration-with-hrworks-single-sign-on"></a>Tutorial: Integração do Azure Active Directory ao HRworks Single Sign-On

Neste tutorial, você aprenderá a integrar o HRworks Single Sign-On ao Azure Active Directory (Azure AD).
A integração do HRworks Single Sign-On ao Azure AD te oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao HRworks Single Sign-On.
* Você pode permitir que os usuários sejam conectados automaticamente ao HRworks Single Sign-On (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao HRworks Single Sign-On, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do HRworks Single Sign-On habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O HRworks Single Sign-On dá suporte ao SSO **SP** iniciado

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Adicionando o HRworks Single Sign-On por meio da galeria

Para configurar a integração do HRworks Single Sign-On ao Azure AD, você precisará adicionar o HRworks Single Sign-On da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HRworks Single Sign-On da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **HRworks Single Sign-On**, selecione **HRworks Single Sign-On** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![HRworks Single Sign-On na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o HRworks Single Sign-On, com base em um usuário de teste chamado **Britta Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HRworks Single Sign-On.

Para configurar e testar o logon único do Azure AD com o HRworks Single Sign-On, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do HRworks Single Sign-On](#configure-hrworks-single-sign-on-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do HRworks Single Sign-On](#create-hrworks-single-sign-on-test-user)** – para ter um equivalente de Britta Simon no HRworks Single Sign-On vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o HRworks Single Sign-On, execute as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **HRworks Single Sign-On**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do HRworks Single Sign-On](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do HRworks Single Sign-On](mailto:support@hrworks.de) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar HRworks Single Sign-On**, copie a(s) URL(s) apropriada(s), de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-hrworks-single-sign-on-single-sign-on"></a>Configurar uma assinatura do HRworks Single Sign-On habilitada para logon único

1. Em uma janela diferente do navegador da Web, entre no HRworks Single Sign-On como Administrador.

2. Clique em **Administrator** > **Básico** > **Segurança** > **Single Sign-on** do lado esquerdo da barra de menus e execute as seguintes etapas:

    ![Configurar o logon único](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Marque a caixa **Usar o Single Sign-on**.

    b. Selecione **Metadados XML** como **Método de entrada de metadados**.

    c. Selecione **Identificador Individual NameID** como **Valor de NameID**.

    d. No Bloco de Notas, abra o arquivo XML de Metadados baixado do portal do Azure, copie seu conteúdo e, em seguida, cole-o na caixa de texto **Metadados**.

    e. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite o nome de usuário como BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Britta Simon use o logon único do Azure ao conceder acesso ao HRworks Single Sign-On.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, selecione **HRworks Single Sign-On**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **HRworks Single Sign-On**.

    ![O link de logon único do HRworks Single Sign-On na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Criar um usuário de teste do HRworks Single Sign-On

Para permitir que os usuários do Azure AD entrem no HRworks Single Sign-On, eles devem ser provisionados no HRworks Single Sign-On. No HRworks Single Sign-On, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no HRworks Single Sign-On como Administrador.

2. Clique em **Administrador** > **Pessoas** > **Pessoas** > **Nova pessoa** do lado esquerdo da barra de menus.

     ![Configurar o logon único](./media/hrworks-single-sign-on-tutorial/configure02.png)

3. No pop-up, clique em **Próxima**.

    ![Configurar o logon único](./media/hrworks-single-sign-on-tutorial/configure03.png)

4. Sobre o pop-up **Criar nova pessoa com país para termos legais**, preencha os respectivos detalhes como **Primeiro nome**, **Sobrenome** e clique em **Criar**.
    
    ![Configurar o logon único](./media/hrworks-single-sign-on-tutorial/configure04.png)

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do HRworks Single Sign-On no Painel de Acesso, você deverá entrar automaticamente no HRworks Single Sign-On para o qual tiver configurado o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


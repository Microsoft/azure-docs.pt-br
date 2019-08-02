---
title: 'Tutorial: Integração do Azure Active Directory com o Workteam | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 697f5c06d2c1d6b669cfa244f0328f4fb86aeea2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086826"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Tutorial: Integração do Azure Active Directory com o Workteam

Neste tutorial, você aprenderá a integrar o Workteam com o Azure AD (Azure Active Directory).
A integração do Workteam com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Workteam.
* Você pode permitir que os usuários sejam conectados automaticamente ao Workteam (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Workteam, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Workteam habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Workteam dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-workteam-from-the-gallery"></a>Adicionando o Workteam da galeria

Para configurar a integração do Workteam ao Azure AD, você precisa adicionar o Workteam da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workteam da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Workteam**, selecione **Workteam** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Workteam na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Workteam com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Workteam.

Para configurar e testar o logon único do Azure AD com o Workteam, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Workteam](#configure-workteam-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Workteam](#create-workteam-test-user)** – para ter um equivalente de Brenda Fernandes no Workteam vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Workteam, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Workteam**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica de SAML**, se você desejar configurar o aplicativo no modo iniciado pelo **IDP** , o usuário não precisará executar nenhuma etapa, pois o aplicativo já estará pré-integrado ao Azure.

    ![Informações de domínio e de URLs do Workteam para logon único](common/preintegrated.png)

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de domínio e de URLs do Workteam para logon único](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.workte.am`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Workteam**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-workteam-single-sign-on"></a>Configurar o logon único do Workteam

1. Em um navegador da Web diferente, entre no Workteam como Administrador de Segurança.

2. No canto superior direito, clique no **logotipo do perfil** e, em seguida, em **Configurações da organização**. 

    ![Configurações do Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

3. Na seção **AUTENTICAÇÃO**, clique no **Logotipo de configurações**.

     ![Workteam Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

4. Na página **Configurações de SAML** , execute as seguintes etapas:

     ![Workteam SAML](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Selecione o **IdP do SAML** como **Azure AD**.

    b. Na caixa de texto **URL de Logon Único do SAML**, cole o valor da **URL de Logon**, copiado do portal do Azure.

    c. Na caixa de texto **ID da Entidade SAML**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    d. No Bloco de Notas, abra o **certificado codificado em Base64** baixado do portal do Azure, copie seu conteúdo e cole-o na caixa **Certificado de Autenticação SAML (Base64)** .

    e. Clique em **OK**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo o acesso ao Workteam.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Workteam**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Workteam**.

    ![O link do Workteam na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-workteam-test-user"></a>Criar usuário de teste do Workteam

Para permitir que os usuários do Azure AD entrem no Workteam, eles precisam ser provisionados no Workteam. No Workteam, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no Workteam como Administrador de Segurança.

2. Na parte superior central da página **Configurações da organização**, clique em **USUÁRIOS** e, em seguida, em **NOVO USUÁRIO**.

    ![Usuário do Workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Na página **Novo funcionário**, realize as seguintes etapas:

    ![Novo usuário do Workteam](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Na caixa de texto **Nome**, insira o nome do usuário como **Brendafernandes**.

    b. Na caixa de texto **Email**, insira o email do usuário como **Brendafernandes\@contoso.com**.

    c. Clique em **OK**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Workteam no Painel de Acesso, você deverá ser conectado automaticamente ao Workteam para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


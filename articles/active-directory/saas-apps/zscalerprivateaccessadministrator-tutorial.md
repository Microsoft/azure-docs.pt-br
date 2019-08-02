---
title: 'Tutorial: Integração do Azure Active Directory ao Zscaler Private Access Administrator | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Administrador do Zscaler Private Access.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7f0bc04bb8e7358f5bdb2e4dfc3db198e24303f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67085636"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Tutorial: Integração do Azure Active Directory ao Zscaler Private Access Administrator

Neste tutorial, você aprenderá a integrar o Administrador do Zscaler Private Access ao Azure AD (Azure Active Directory).
A integração do Administrador do Zscaler Private Access ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Administrador do Zscaler Private Access.
* Você pode permitir que os usuários sejam conectados automaticamente ao Zscaler Private Access Administrator (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Administrador do Zscaler Private Access, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Zscaler Private Access Administrator

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zscaler Private Access Administrator dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Adicionando o Administrador do Zscaler Private Access da galeria

Para configurar a integração do Administrador do Zscaler Private Access ao Azure AD, você precisará adicionar o Administrador do Zscaler Private Access da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Administrador do Zscaler Private Access da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Administrador do Zscaler Private Access**, selecione **Administrador do Zscaler Private Access** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Administrador do Zscaler Private Access na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zscaler Private Access Administrator, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zscaler Private Access Administrator.

Para configurar e testar o logon único do Azure AD com o Administrador do Zscaler Private Access, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Zscaler Private Access Administrator](#configure-zscaler-private-access-administrator-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Zscaler Private Access Administrator](#create-zscaler-private-access-administrator-test-user)** – para ter um equivalente de Brenda Fernandes no Zscaler Private Access Administrator que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Zscaler Private Access Administrator, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zscaler Private Access Administrator**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Administrador do Zscaler Private Access](common/idp-relay.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Clique em **Definir URLs adicionais**.

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `idpadminsso`

5.  Se quiser configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Administrador do Zscaler Private Access](common/both-signonurl.png)

    Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o Zscaler Private Access Administrator**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Configurar o logon único do Zscaler Private Access Administrator

1. Em outra janela do navegador da Web, entre no Zscaler Private Access Administrator como Administrador.

2. Na parte superior, clique em **Administração**, navegue até a seção **AUTENTICAÇÃO** e clique em **Configuração de IdP**.

    ![Administrador de Administrador do Zscaler Private Access](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. No canto superior direito, clique em **Adicionar Configuração de IdP**. 

    ![Addidp do Administrador do Zscaler Private Access](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Na página **Adicionar Configuração de IdP**, execute as seguintes etapas:
 
    ![Idpselect do Administrador do Zscaler Private Access](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Clique em **Selecionar Arquivo** para carregar o arquivo de metadados baixado do Azure AD no campo **Upload de Arquivo de Metadados de IdP**.

    b. Ele lê os **metadados de IdP** do Azure AD e popula todas as informações dos campos, conforme mostrado abaixo.

    ![Idpconfig do Administrador do Zscaler Private Access](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Selecione **Logon Único** como **Administrador**.

    d. Selecione seu domínio do campo **Domínios**.
    
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
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Administrador do Zscaler Private Access.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Zscaler Private Access Administrator**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Administrador do Zscaler Private Access**.

    ![O link do Administrador do Zscaler Private Access na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Criar um usuário de teste do Zscaler Private Access Administrator

Para permitir que os usuários do Azure AD entrem no Zscaler Private Access Administrator, eles precisam ser provisionados no Zscaler Private Access Administrator. No caso do Administrador do Zscaler Private Access, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do Zscaler Private Access Administrator como administrador.

2. Na parte superior, clique em **Administração**, navegue até a seção **AUTENTICAÇÃO** e clique em **Configuração de IdP**.

    ![Administrador de Administrador do Zscaler Private Access](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Clique em **Administradores** do lado esquerdo do menu.

    ![Administrador do Administrador do Zscaler Private Access](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. No canto superior direito, clique em **Adicionar Administrador**:

    ![Adicionar administrador do Administrador do Zscaler Private Access](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Na página **Adicionar Administrador**, realize as seguintes etapas:

    ![Administrador de usuários do Administrador do Zscaler Private Access](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Na caixa de texto **Nome de usuário**, insira o email do usuário como BrittaSimon@contoso.com.

    b. Na caixa de texto **Senha**, digite a senha.

    c. Na caixa de texto **Confirmar Senha**, digite a senha.

    d. Selecione **Função** como **Administrador do Zscaler Private Access**.

    e. Na caixa de texto **Email**, insira o email do usuário como BrittaSimon@contoso.com.

    f. Na caixa de texto **Telefone**, digite o número de telefone.

    g. Na caixa de texto **Fuso Horário**, selecione o fuso horário.

    h. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Zscaler Private Access Administrator no Painel de Acesso, você deverá ser conectado automaticamente ao Zscaler Private Access Administrator, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


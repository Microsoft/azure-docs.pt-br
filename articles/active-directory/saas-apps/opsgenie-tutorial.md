---
title: 'Tutorial: Integração do Azure Active Directory ao OpsGenie | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 446ac54d84f7b2b3bf3aaf6eaf5536f0dfb804fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095763"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutorial: Integração do Azure Active Directory ao OpsGenie

Neste tutorial, você aprenderá a integrar o OpsGenie ao Azure AD (Azure Active Directory).
A integração do OpsGenie ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao OpsGenie.
* Você pode permitir que seus usuários entrem automaticamente no OpsGenie (logon único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao OpsGenie, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do OpsGenie habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O OpsGenie dá suporte a SSO iniciado por **SP**

## <a name="adding-opsgenie-from-the-gallery"></a>Adição do OpsGenie da galeria

Para configurar a integração do OpsGenie ao Azure AD, você precisa adicionar o OpsGenie a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OpsGenie a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **OpsGenie**, selecione **OpsGenie** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![OpsGenie na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o OpsGenie com base em um usuário de teste chamado **B. Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do OpsGenie.

Para configurar e testar o logon único do Azure AD com o OpsGenie, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do OpsGenie](#configure-opsgenie-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B. Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do OpsGenie](#create-opsgenie-test-user)** – para ter um equivalente de B. Fernandes no OpsGenie que esteja vinculado à representação desse usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o OpsGenie, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **OpsGenie**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e de URLs do OpsGenie](common/sp-signonurl.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.opsgenie.com/auth/login`

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

6. Na seção **Configurar o OpsGenie**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-opsgenie-single-sign-on"></a>Configurar o logon único do OpsGenie

1. Abra outra instância do navegador e, em seguida, faça logon no OpsGenie como administrador.

2. Clique em **Configurações** e na guia **Logon único**.
   
    ![Logon único do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Para habilitar o SSO, selecione **Habilitado**.
   
    ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. Na seção **Provedor**, clique na guia **Azure Active Directory**.
   
    ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Na página de diálogo Azure Active Directory, execute as seguintes etapas:
   
    ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Na caixa de texto **Ponto de Extremidade do SAML 2.0**, cole o valor da **URL de Logon**copiado do portal do Azure.
    
    b. Na caixa de texto **URL de Metadados:** , cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure.
    
    c. Clique em **Salvar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado B. Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **B. Fernandes**.
  
    b. No campo **Nome de usuário**, digite **bsimon@yourcompanydomain.extension**  
    Por exemplo, BSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo-lhe acesso ao OpsGenie.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **OpsGenie**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **OpsGenie**.

    ![O link do OpsGenie na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **B. Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-opsgenie-test-user"></a>Criar um usuário de teste do OpsGenie

O objetivo desta seção é criar um usuário chamado B. Fernandes no OpsGenie. 

1. Em uma janela de navegador da Web, faça logon em seu locatário do OpsGenie como administrador.

2. Navegue até a lista Usuários clicando em **Usuários** no painel esquerdo.
   
    ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Clique em **Adicionar Usuário**.

4. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
   
    ![Configurações do OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Na caixa de texto **Email**, digite o endereço de email de B. Fernandes endereçado no Azure Active Directory.
   
    b. Na caixa de texto **Nome Completo**, digite **B. Fernandes**.
   
    c. Clique em **Save** (Salvar). 

>[!NOTE]
>B. Brenda receberá um email com instruções para configurar o perfil dela.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do OpsGenie no Painel de Acesso, você deverá ser conectado automaticamente ao OpsGenie para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


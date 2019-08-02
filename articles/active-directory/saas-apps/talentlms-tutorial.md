---
title: 'Tutorial: Integração do Azure Active Directory com o TalentLMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0243a3e0ed83abc1edead5ecece4fd5c6ff1cad9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089157"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Integração do Azure Active Directory com o TalentLMS

Neste tutorial, você aprenderá a integrar o TalentLMS ao Azure AD (Azure Active Directory).
A integração do TalentLMS com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao TalentLMS.
* É possível permitir que os usuários entrem automaticamente no TalentLMS (Logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o TalentLMS, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do TalentLMS habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O TalentLMS é compatível com o SSO iniciado por **SP**

## <a name="adding-talentlms-from-the-gallery"></a>Adicionar o TalentLMS da galeria

Para configurar a integração do TalentLMS com o Azure AD, é necessário adicionar o TalentLMS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TalentLMS da galeria, siga as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TalentLMS**, selecione **TalentLMS** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![TalentLMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o TalentLMS, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TalentLMS.

Para configurar e testar o logon único do Azure AD com o TalentLMS, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do TalentLMS](#configure-talentlms-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do TalentLMS](#create-talentlms-test-user)** – para ter um equivalente de Brenda Fernandes no TalentLMS que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TalentLMS, siga as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **TalentLMS**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e URLs do TalentLMS](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.TalentLMSapp.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do TalentLMS](https://www.talentlms.com/contact) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

6. Na seção **Certificado de Autenticação SAML**, copie a **IMPRESSÃO DIGITAL** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

7. Na seção **Configurar o TalentLMS**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-talentlms-single-sign-on"></a>Configurar o logon único do TalentLMS

1. Em outra janela do navegador da Web, entre no site da empresa TalentLMS como administrador.

1. Na seção **Conta e Configurações**, clique na guia **Usuários**.

    ![Conta e Configurações](./media/talentlms-tutorial/IC777296.png "Conta e Configurações")

1. Clique em **SSO (Logon Único)** .

1. Na seção de Configurações de Logon Único, execute as seguintes etapas:

    ![Logon Único](./media/talentlms-tutorial/IC777297.png "Logon Único")

    a. Na lista **Tipo de integração de SSO**, selecione **SAML 2.0**.

    b. Na caixa de texto **ID do Provedor de Identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Cole o valor da **Impressão digital** do Portal do Azure na caixa de texto **Impressão digital do certificado**.

    d.  Na caixa de texto **URL de logon remoto**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Na caixa de texto **URL de logoff remoto**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    f. Preencha o seguinte:

    * Na caixa de texto **TargetedID**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Na caixa de texto **Nome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Na caixa de texto **Sobrenome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TalentLMS.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **TalentLMS**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TalentLMS**.

    ![O link do TalentLMS na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-talentlms-test-user"></a>Criar um usuário de teste do TalentLMS

Para permitir que os usuários do Azure AD entre no TalentLMS, eles devem ser provisionados no TalentLMS. No caso do TalentLMS, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu locatário do **TalentLMS**.

1. Clique em **Usuários** e em **Adicionar Usuário**.

1. Na página do diálogo **Adicionar usuário** , realize as seguintes etapas:

    ![Adicionar Usuário](./media/talentlms-tutorial/IC777299.png "Adicionar Usuário")  

    a. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.
 
    c. Na caixa de texto **Endereço de email**, insira o email do usuário como `brittasimon\@contoso.com`.

    d. Clique em **Adicionar Usuário**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do TalentLMS ou as APIs fornecidas pelo TalentLMS para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do TalentLMS no Painel de Acesso, você deverá ser conectado automaticamente ao TalentLMS, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


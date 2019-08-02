---
title: 'Tutorial: Integração do Azure Active Directory com o Bonusly | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f605398896c93d15b7475c05c79361fdcefcfcab
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442768"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integração do Azure Active Directory com o Bonusly

Neste tutorial, você aprenderá a integrar o Bonusly ao Azure AD (Azure Active Directory).
A integração do Bonusly ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Bonusly.
* Você pode permitir que seus usuários entrem automaticamente no Bonusly (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Bonusly, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do Bonusly habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Bonusly é compatível com SSO iniciado por **IDP**

## <a name="adding-bonusly-from-the-gallery"></a>Adicionando o Bonusly da galeria

Para configurar a integração do Bonusly ao Azure AD, você precisa adicionar o Bonusly da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Bonusly da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Bonusly**, selecione **Bonusly** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Bonusly na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Bonusly, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Bonusly.

Para configurar e testar o logon único do Azure AD com o Bonusly, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Bonusly](#configure-bonusly-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Bonusly](#create-bonusly-test-user)** – para ter um equivalente de Brenda Fernandes no Bonusly vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Bonusly, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Bonusly**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Bonusly](common/idp-reply.png)

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte ao cliente do Bonusly](https://bonus.ly/contact) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

6. Na seção **Certificado de Autenticação SAML**, copie a **IMPRESSÃO DIGITAL** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

7. Na seção **Configurar o Bonusly**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-bonusly-single-sign-on"></a>Configurar o logon único do Bonusly

1. Em outra janela do navegador, conecte-se ao seu locatário do **Bonusly**.

1. Na barra de ferramentas na parte superior, clique em **Configurações** e selecione **Integrações e aplicativos**.

    ![Seção Social Bonusly](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Em **Logon Único**, selecione **SAML**.

1. Na página do diálogo **SAML** , realize as seguintes etapas:

    ![Página de Diálogo Saml do Bonusly](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. Na caixa de texto **URL de Destino de SSO de IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Na caixa de texto **URL de Logon de IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **Emissor do IdP**, cole o valor de **Identificador do Azure AD** copiado do portal do Azure.
    
    d. Cole o valor da **Impressão digital** copiado do Portal do Azure na caixa de texto **Impressão Digital do Certificado**.
    
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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Bonusly.

1. No portal do Azure, escolha **Aplicativos Empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **Bonusly**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha o **Bonusly**.

    ![O link do Bonusly na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-bonusly-test-user"></a>Criar um usuário de teste do Bonusly

Para permitir que os usuários do Azure AD se conectem ao Bonusly, eles precisam estar provisionados no Bonusly. No caso do Bonusly, o provisionamento é uma tarefa manual.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Bonusly ou as APIs fornecidas pelo Bonusly para provisionar as contas de usuário do AAD. 

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Em uma janela do navegador da Web, conecte-se ao seu locatário do Bonusly.

1. Clique em **Configurações**.

    ![Configurações](./media/bonus-tutorial/ic781041.png "Configurações")

1. Clique na guia **Usuários e bônus** .

    ![Usuários e bônus](./media/bonus-tutorial/ic781042.png "Usuários e bônus")

1. Clique em **Gerenciar Usuários**.

    ![Gerenciar Usuários](./media/bonus-tutorial/ic781043.png "Gerenciar Usuários")

1. Clique em **Adicionar Usuário**.

    ![Adicionar Usuário](./media/bonus-tutorial/ic781044.png "Adicionar Usuário")

1. No diálogo **Adicionar Usuário** , realize as seguintes etapas:

    ![Adicionar Usuário](./media/bonus-tutorial/ic781045.png "Adicionar Usuário")  

    a. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    c. Na caixa de texto **Email**, insira o email do usuário como `brittasimon\@contoso.com`.

    d. Clique em **Save** (Salvar).

    > [!NOTE]
    > O titular da conta do Azure AD recebe um email com um link de confirmação de conta antes que ela se torne ativa.  

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Bonusly no Painel de Acesso, você deverá ser conectado automaticamente ao Bonusly no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

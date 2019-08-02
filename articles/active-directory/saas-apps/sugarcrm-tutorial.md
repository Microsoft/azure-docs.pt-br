---
title: 'Tutorial: Integração do Azure Active Directory ao Sugar CRM | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 5c46b096b1182b3f2a998f992f72e6127dfe0888
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089548"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Tutorial: Integração do Azure Active Directory ao Sugar CRM

Neste tutorial, você aprenderá a integrar o Sugar CRM ao Azure AD (Azure Active Directory).
A integração do Sugar CRM ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Sugar CRM.
* Você pode permitir que os usuários sejam conectados automaticamente ao Sugar CRM (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Sugar CRM, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Sugar CRM

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Sugar CRM dá suporte a SSO iniciado por **SP**

## <a name="adding-sugar-crm-from-the-gallery"></a>Adicionar o Sugar CRM da galeria

Para configurar a integração do Sugar CRM ao Azure AD, você precisará adicionar o Sugar CRM da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Sugar CRM da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Sugar CRM**, selecione **Sugar CRM** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Sugar CRM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Sugar CRM, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Sugar CRM.

Para configurar e testar o logon único do Azure AD com o Sugar CRM, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Sugar CRM](#configure-sugar-crm-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Sugar CRM](#create-sugar-crm-test-user)** – para ter um equivalente de Brenda Fernandes no Sugar CRM que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Sugar CRM, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Sugar CRM**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Sugar CRM](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate [equipe de suporte ao cliente do Sugar CRM](https://support.sugarcrm.com/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Sugar CRM**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-sugar-crm-single-sign-on"></a>Configurar o logon único do Sugar CRM

1. Em outra janela do navegador da Web, entre em seu site de empresa do Sugar CRM como administrador.

1. Vá para **Administrador**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Na seção **Administração**, clique em **Gerenciamento de Senhas**.

    ![Administração](./media/sugarcrm-tutorial/ic795889.png "Administração")

1. Selecione **Habilitar Autenticação SAML**.

    ![Administração](./media/sugarcrm-tutorial/ic795890.png "Administração")

1. Na seção **Autenticação SAML** , realize as seguintes etapas:

    ![Autenticação SAML](./media/sugarcrm-tutorial/ic795891.png "Autenticação SAML")  

    a. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.
  
    b. Na caixa de texto **URL de SLO**, cole o valor da **URL de Logoff** copiado do portal do Azure.
  
    c. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .
  
    d. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Sugar CRM.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Sugar CRM**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Sugar CRM**.

    ![O link do Sugar CRM na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sugar-crm-test-user"></a>Criar um usuário de teste do Sugar CRM

Para permitir que os usuários do Azure AD entrem no Sugar CRM, eles precisam ser provisionados no Sugar CRM. No caso do SugarCRM, o provisionamento será uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do **Sugar CRM** como administrador.

1. Vá para **Administrador**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Na seção **Administração**, clique em **Gerenciamento de Usuários**.

    ![Administração](./media/sugarcrm-tutorial/ic795893.png "Administração")

1. Vá para **Usuários \> Criar Novo Usuário**.

    ![Criar Novo Usuário](./media/sugarcrm-tutorial/ic795894.png "Criar Novo Usuário")

1. Na guia **Perfil do Usuário** , realize as seguintes etapas:

    ![Novo Usuário](./media/sugarcrm-tutorial/ic795895.png "Novo Usuário")

    * Digite o **nome de usuário**, o **sobrenome** e o **endereço de email** de um usuário válido do Azure Active Directory nas caixas de texto relacionadas.
  
1. Para **Status**, selecione **Ativo**.

1. Na guia Senha, execute as seguintes etapas:

    ![Novo Usuário](./media/sugarcrm-tutorial/ic795896.png "Novo Usuário")

    a. Digite a senha na caixa de texto relacionada.

    b. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do SugarCRM ou as APIs fornecidas pelo SugarCRM para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Sugar CRM no Painel de Acesso, você deverá ser conectado automaticamente ao Sugar CRM, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


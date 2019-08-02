---
title: 'Tutorial: Integração do Azure Active Directory com o Work.com | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087091"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integração do Azure Active Directory com o Work.com

Neste tutorial, você aprenderá a integrar o Work.com ao Azure AD (Azure Active Directory).
A integração do Work.com ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Work.com.
* Você pode permitir que os usuários sejam conectados automaticamente ao Work.com (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Work.com, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do Work.com habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Work.com é compatível com SSO iniciado por **SP**

## <a name="adding-workcom-from-the-gallery"></a>Adicionar o Work.com da galeria

Para configurar a integração do Work.com ao Azure AD, você precisará adicionar o Work.com da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Work.com da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Work.com**, selecione **Work.com** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Work.com na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Work.com com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Work.com.

Para configurar e testar o logon único do Azure AD com o Work.com, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Work.com](#configure-workcom-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Work.com](#create-workcom-test-user)** – para ter um equivalente de Brenda Fernandes no Work.com vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

>[!NOTE]
>Para configurar o logon único, você ainda precisa definir um nome de domínio personalizado Work.com. Você precisa definir pelo menos um nome de domínio, testá-lo e implantá-lo em toda a sua organização.

Para configurar o logon único do Azure AD com o Work.com, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Work.com**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Work.com](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Work.com**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-workcom-single-sign-on"></a>Configurar o logon único do Work.com

1. Entre no seu locatário do Work.com como administrador.

2. Vá para **Configuração**.
   
    ![Configuração](./media/work-com-tutorial/ic794108.png "Configuração")

3. No painel de navegação à esquerda, na seção **Administrador**, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e clique em **Meu Domínio** para abrir a página **Meu Domínio**. 
   
    ![Meu Domínio](./media/work-com-tutorial/ic767825.png "Meu Domínio")

4. Para confirmar se o domínio foi configurado corretamente, verifique se ele está em “**Etapa 4 Implantado nos Usuários**” e examine “**Minhas Configurações de Domínio**”.
   
    ![Domínio Implantado no Usuário](./media/work-com-tutorial/ic784377.png "Domínio Implantado no Usuário")

5. Entre no seu locatário do Work.com.

6. Vá para **Configuração**.
    
    ![Configuração](./media/work-com-tutorial/ic794108.png "Configuração")

7. Expanda o menu **Controles de Segurança** e clique em **Configurações de Logon Único**.
    
    ![Configurações de Logon Único](./media/work-com-tutorial/ic794113.png "Configurações de Logon Único")

8. Na página do diálogo **Configurações de Logon Único** , realize as seguintes etapas:
    
    ![SAML habilitado](./media/work-com-tutorial/ic781026.png "SAML habilitado")
    
    a. Selecione **SAML Habilitado**.
    
    b. Clique em **Novo**.

9. Na seção de **Configurações de Logon Único do SAML** , realize as seguintes etapas:
    
    ![Configurações de Logon Único do SAML](./media/work-com-tutorial/ic794114.png "Configurações de Logon Único do SAML")
    
    a. Na caixa de texto **Nome** , digite um nome para a sua configuração.  
       
    > [!NOTE]
    > Fornecer um valor para **Nome** popula automaticamente a caixa de texto **Nome da API**.
    
    b. Na caixa de texto **Emissor**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.
    
    c. Para carregar o certificado baixado do Portal do Azure, clique em **Procurar**.
    
    d. Na caixa de texto **ID da Entidade**, digite `https://salesforce-work.com`.
    
    e. Para o **Tipo de Identidade SAML**, selecione **A declaração contém a ID de Federação do objeto de Usuário**.
    
    f. Para **Local de Identidade do SAML**, selecione **A identidade está contida no elemento NameIdentifier da instrução Subject**.
    
    g. Na caixa de texto **URL de Logon do Provedor de Identidade**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    h. Na caixa de texto **URL de Logoff do Provedor de Identidade**, cole o valor de **URL de Logoff** que você copiou do portal do Azure.
    
    i. Para **Associação de Solicitação Iniciada pelo Provedor de Serviços**, selecione **HTTP Post**.
    
    j. Clique em **Save** (Salvar).

10. No painel de navegação à esquerda, no portal clássico do Work.com, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e clique em **Meu Domínio** para abrir a página **Meu Domínio**. 
    
    ![Meu Domínio](./media/work-com-tutorial/ic794115.png "Meu Domínio")

11. Na página **Meu Domínio**, na seção **Identidade Visual da Página de Logon**, clique em **Editar**.
    
    ![Identidade Visual da Página de Logon](./media/work-com-tutorial/ic767826.png "Identidade Visual da Página de Logon")

12. Na página **Identidade Visual da Página de Logon**, na seção **Serviço de Autenticação**, o nome das **Configurações de SSO do SAML** é exibido. Selecione-o e, em seguida, clique em **Salvar**.
    
    ![Identidade Visual da Página de Logon](./media/work-com-tutorial/ic784366.png "Identidade Visual da Página de Logon")

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Work.com.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Work.com**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Work.com**.

    ![O link do Work.com na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-workcom-test-user"></a>Criar usuário de teste do Work.com

Para que os usuários do Active Directory do Azure possam entrar, eles devem ser provisionados para Work.com. No caso do Work.com, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. Faça logon no site da sua empresa do Work.com como um administrador.

2. Vá para **Configuração**.
   
    ![Configuração](./media/work-com-tutorial/IC794108.png "Configuração")

3. Vá para **Gerenciar Usuários \> Usuários**.
   
    ![Gerenciar Usuários](./media/work-com-tutorial/IC784369.png "Gerenciar Usuários")

4. Clique em **Novo Usuário**.
   
    ![Todos os Usuários](./media/work-com-tutorial/IC794117.png "Todos os Usuários")

5. Na seção Editar Usuários, execute as etapas a seguir nos atributos de uma conta do Azure AD válida que você deseja provisionar nas caixas de texto relacionadas:
   
    ![Editar Usuário](./media/work-com-tutorial/ic794118.png "Editar Usuário")
   
    a. Na caixa de texto **Nome**, digite o **nome** do usuário, **Brenda**.
    
    b. Na caixa de texto **Sobrenome**, digite o **sobrenome** do usuário, **Fernandes**.
    
    c. Na caixa de texto **Alias**, digite o **nome** do usuário, **BrendaF**.
    
    d. Na caixa de texto **Email**, digite o **endereço de email** do usuário, Brittasimon@contoso.com.
    
    e. Na caixa de texto **Nome de Usuário**, digite um nome de usuário do usuário, como Brittasimon@contoso.com.
    
    f. Na caixa de texto **Apelido**, digite um **apelido** para o usuário, assim como **Fernandes**.
    
    g. Selecione **Função**, **Licença de Usuário** e **Perfil**.
    
    h. Clique em **Save** (Salvar).  
      
    > [!NOTE]
    > O titular da conta do Azure AD receberá um email com um link de confirmação de conta para que ela se torne ativa.
    > 

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Work.com no Painel de Acesso, você deverá ser conectado automaticamente ao Work.com, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


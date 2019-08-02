---
title: 'Tutorial: Integração do Azure Active Directory ao Citrix ShareFile | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9904ba14e8d5a518807eda371ba655963b33bd4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091019"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integração do Azure Active Directory ao Citrix ShareFile

Nesse tutorial, você aprenderá a integrar o Citrix ShareFile ao Azure AD (Azure Active Directory).
A integração do Citrix ShareFile ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Citrix ShareFile.
* Você pode permitir que os usuários sejam conectados automaticamente ao Citrix ShareFile (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Citrix ShareFile, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Citrix ShareFile

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Citrix ShareFile dá suporte ao SSO iniciado por **SP**

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adicionar o Citrix ShareFile da galeria

Para configurar a integração do Citrix ShareFile com o Azure AD, você precisa adicionar o Citrix ShareFile, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Citrix ShareFile da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Citrix ShareFile**, selecione **Citrix ShareFile** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Citrix ShareFile na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Citrix ShareFile, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Citrix ShareFile.

Para configurar e testar o logon único do Azure AD com o Citrix ShareFile, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Citrix ShareFile](#configure-citrix-sharefile-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Citrix ShareFile](#create-citrix-sharefile-test-user)** – para ter um equivalente de Brenda Fernandes no Citrix ShareFile que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Citrix ShareFile, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Citrix ShareFile**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Citrix ShareFile](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<tenant-name>.sharefile.com/saml/login`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Citrix ShareFile**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-citrix-sharefile-single-sign-on"></a>Configurar o Logon Único do Citrix ShareFile

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do **Citrix ShareFile** como administrador.

2. Na barra de ferramentas na parte superior, clique em **Administrador**.

3. No painel de navegação à esquerda, selecione **Configurar Logon Único**.
   
    ![Administração de Conta](./media/sharefile-tutorial/ic773627.png "Administração de Conta")

4. Na página **Configuração de Logon Único/SAML 2.0** em **Configurações Básicas**, realize as seguintes etapas:
   
    ![Logon Único](./media/sharefile-tutorial/ic773628.png "Logon Único")
   
    a. Clique em **Habilitar SAML**.
    
    b. Na caixa de texto **Emissor IDP/ID da Entidade**, cole o valor de **Identificador do Azure AD** copiado no portal do Azure.

    c. Clique em **Alterar** ao lado do campo **Certificado X.509** e carregue o certificado baixado no portal Azure.
    
    d. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado no portal do Azure.
    
    e. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado no portal do Azure.

5. Clique em **Salvar** no portal de gerenciamento do Citrix ShareFile.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Citrix ShareFile.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Citrix ShareFile**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Citrix ShareFile**.

    ![O link do Citrix ShareFile na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-citrix-sharefile-test-user"></a>Criar um usuário de teste do Citrix ShareFile

Para permitir que os usuários do Azure AD façam logon no Citrix ShareFile, eles devem ser provisionados no Citrix ShareFile. No caso do Citrix ShareFile, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Citrix ShareFile** .

2. Clique em **Gerenciar Usuários \> Gerenciar Página Inicial dos Usuários \> + Criar Funcionário**.
   
    ![Criar Funcionário](./media/sharefile-tutorial/IC781050.png "Criar Funcionário")

3. Na seção **Informações Básicas**, execute as etapas abaixo:
   
    ![Informações Básicas](./media/sharefile-tutorial/IC799951.png "Informações Básicas")
   
    a. Na caixa de texto **Endereço de Email**, digite o endereço de email de Brenda Fernandes como **brendafernandes\@contoso.com**.
   
    b. Na caixa de texto **Nome**, digite o **nome** do usuário como **Brenda**.
   
    c. Na caixa de texto **Sobrenome**, digite o **sobrenome** do usuário como **Fernandes**.

4. Clique em **Adicionar Usuário**.
  
    >[!NOTE]
    >O titular da conta do Azure AD receberá um email e seguirá um link para confirmar sua conta antes de se tornar ativo. Você pode usar quaisquer outras ferramentas de criação de conta de usuário do Citrix ShareFile ou APIs fornecidas pelo Citrix ShareFile para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Citrix ShareFile no Painel de Acesso, você deverá ser conectado automaticamente ao Citrix ShareFile, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


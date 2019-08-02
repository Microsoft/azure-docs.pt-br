---
title: 'Tutorial: Integração do Azure Active Directory ao Egnyte | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 808564f291328450b17db8eb7ea299c194c66400
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103532"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Tutorial: Integração do Azure Active Directory ao Egnyte

Neste tutorial, você aprenderá a integrar o Egnyte ao Azure AD (Azure Active Directory).
A integração do Egnyte ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Egnyte.
* Você pode permitir que os usuários sejam conectados automaticamente ao Egnyte (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Egnyte, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Egnyte

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Egnyte dá suporte ao SSO iniciado por **SP**

## <a name="adding-egnyte-from-the-gallery"></a>Adicionando Egnyte da galeria

Para configurar a integração do Egnyte ao Azure AD, você precisará adicionar o Egnyte da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Egnyte da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Egnyte**, selecione **Egnyte** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Egnyte na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Egnyte, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Egnyte.

Para configurar e testar o logon único do Azure AD com o Egnyte, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Egnyte](#configure-egnyte-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Egnyte](#create-egnyte-test-user)** – para ter um equivalente de Brenda Fernandes no Egnyte que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Egnyte, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Egnyte**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de domínio único do Egnyte Domain e URLs](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

5. Na seção **Configurar o Egnyte**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-egnyte-single-sign-on"></a>Configurar o logon único do Egnyte

1. Em uma janela diferente do navegador da Web, faça logon no site da empresa do Egnyte como administrador.

2. Clique em **Configurações**.
   
    ![Configurações](./media/egnyte-tutorial/ic787819.png "Configurações")

3. No menu, clique em **Configurações**.

    ![Configurações](./media/egnyte-tutorial/ic787820.png "Configurações")

4. Clique na guia **Configuração** e, em seguida, clique em **Segurança**.

    ![Segurança](./media/egnyte-tutorial/ic787821.png "Segurança")

5. Na seção **Autenticação de Logon Único** , realize as seguintes etapas:

    ![Autenticação de Logon Único](./media/egnyte-tutorial/ic787822.png "Autenticação de Logon Único")   
    
    a. Para **Autenticação de logon único**, selecione **SAML 2.0**.
   
    b. Para **Provedor de identidade**, selecione **AzureAD**.
   
    c. Cole o **URL de login** copiado do portal do Azure na caixa de texto **URL de login do provedor de identidade**.
   
    d. Cole o **Identificador do Azure AD** que você copiou do portal do Azure na caixa de texto **Identidade da entidade do provedor de identidade**.
      
    e. Abra seu certificado codificado em base-64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado do provedor de identidade**.
   
    f. Para **Mapeamento de usuário padrão**, selecione **Endereço de email**.
   
    g. Para **Usar valor do emissor específico do domínio**, selecione **desabilitado**.
   
    h. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Egnyte.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Egnyte**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Egnyte**.

    ![O link do Egnyte na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-egnyte-test-user"></a>Criar um usuário de teste do Egnyte

Para permitir que os usuários do Azure AD façam logon no Egnyte, eles deverão ser provisionados no Egnyte. No caso do Egnyte, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon no site de empresa do **Egnyte** como administrador.

2. Vá para **Configurações \> Usuários e Grupos**.

3. Clique em **Adicionar Novo Usuário**e selecione o tipo de usuário que deseja adicionar.
   
    ![Usuários](./media/egnyte-tutorial/ic787824.png "Usuários")

4. Na seção **Novo Usuário Avançado**, execute as seguintes etapas:
    
    ![Novo Usuário Padrão](./media/egnyte-tutorial/ic787825.png "Novo Usuário Padrão")   

    a. Na caixa de texto **Email**, insira o email do usuário como **Brendafernandes\@contoso.com**.

    b. Na caixa de texto **Username**, insira o nome de usuário como **Brittasimon**.

    c. Selecione **logon único** como **tipo de autenticação**.
   
    d. Clique em **Save** (Salvar).
    
    >[!NOTE]
    >O titular da conta do Active Directory do Azure receberá um email de notificação.
    >

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Egnyte ou as APIs fornecidas pelo Egnyte para provisionar as contas de usuário do AAD.
>

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Egnyte no Painel de Acesso, você deverá ser conectado automaticamente ao Egnyte, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


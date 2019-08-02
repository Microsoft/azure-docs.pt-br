---
title: 'Tutorial: Integração do Azure Active Directory ao Questetra BPM Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f58d6cbc6ec04e51e105662dff31c60ff502584c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093353"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração do Azure Active Directory ao Questetra BPM Suite

Neste tutorial, você aprenderá a integrar o Questetra BPM Suite ao Azure AD (Azure Active Directory).
A integração do Questetra BPM Suite ao Azure AD oferece os seguintes benefícios:

* Você pode controlar, no Azure AD, quem tem acesso ao Questetra BPM Suite.
* Você pode permitir que seus usuários entrem automaticamente no Questetra BPM Suite (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o Questetra BPM Suite, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Questetra BPM Suite

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Questetra BPM Suite é compatível com SSO iniciado por **SP**

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Adicionar um Questetra BPM Suite da galeria

Para configurar a integração do Questetra BPM Suite com o AD do Azure, você precisa adicionar o Questetra BPM Suite, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Questetra BPM Suite por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Questetra BPM Suite**, selecione **Questetra BPM Suite** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Questetra BPM Suite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Questetra BPM Suite com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Questetra BPM Suite.

Para configurar e testar o logon único do AD do Azure com o Questetra BPM Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Questetra BPM Suite](#configure-questetra-bpm-suite-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Questetra BPM Suite](#create-questetra-bpm-suite-test-user)** – para ter um equivalente de Brenda Fernandes no Questetra BPM Suite que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Questetra BPM Suite, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Questetra BPM Suite**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Questetra BPM Suite](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Você pode obter esses valores na seção **Informações de SP** no site de empresa de **Questetra BPM Suite**, o que é explicado posteriormente no tutorial, ou entre em contato com a [equipe de suporte ao cliente do Questetra BPM Suite](https://www.questetra.com/contact/). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Questetra BPM Suite**, copie as URLs apropriadas, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Configurar logon único do Questetra BPM Suite

1. Em outra janela do navegador da Web, entre em seu site de empresa **Questetra BPM Suite** como administrador.

2. No menu na parte superior, clique em **Configurações do Sistema**. 
   
    ![Logon Único do AD do Azure][10]

3. Para abrir a página **SingleSignOnSAML**, clique em **SSO (SAML)** . 
   
    ![Logon Único do AD do Azure][11]

4. No site de empresa do **Questetra BPM Suite**, na seção **Informações de SP**, execute as etapas a seguir:

    a. Copie a **URL do ACS** e cole-a na caixa de texto **URL de Entrada** na seção **Configuração Básica de SAML** do portal do Azure.
    
    b. Copie a **ID da Entidade** e cole-a na caixa de texto **Identificador** na seção **Configuração Básica de SAML** do portal do Azure.

5. No seu site de empresa **Questetra BPM Suite**, execute as seguintes etapas: 
   
    ![Configurar o logon único][15]
   
    a. Selecione **Habilitar Logon Único**.
   
    b. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** que você copiou do portal do Azure.
    
    c. Na caixa de texto **URL da página de entrada**, cole o valor da **URL de Logon** copiado do portal do Azure.
    
    d. Na caixa de texto **URL da página de saída**, cole o valor da **URL de Logout** copiado do portal do Azure.
    
    e. Na caixa de texto **Formato de NameID**, digite `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Abra o certificado codificado em **Base64** no bloco de notas baixado do Portal do Azure, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado de validação**. 

    g. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Questetra BPM Suite.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Questetra BPM Suite**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Questetra BPM Suite**.

    ![O link do Questetra BPM Suite na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-questetra-bpm-suite-test-user"></a>Criar usuário de teste do Questetra BPM Suite

O objetivo desta seção é criar um usuário chamado Britta Simon no Questetra BPM Suite.

**Para criar um usuário chamado Brenda Fernandes no Questetra BPM Suite, execute as seguintes etapas:**

1. Entre no site de sua empresa do Questetra BPM Suite como administrador.

2. Vá para **Configurações do Sistema > Lista de Usuários > Novo Usuário**.
 
3. Na caixa de diálogo Novo Usuário, execute as seguintes etapas: 
   
    ![Criar um usuário de teste][300] 
   
    a. Na caixa de texto **Nome**, digite o **nome** do usuário britta.simon@contoso.com.
   
    b. Na caixa de texto **Email**, digite o **email** do usuário britta.simon@contoso.com.
   
    c. Na caixa de texto **Senha** , digite uma **senha** para o usuário.
    
    d. Clique em **Adicionar novo usuário**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Questetra BPM Suite no Painel de Acesso, você deverá ser conectado automaticamente ao Questetra BPM Suite no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png
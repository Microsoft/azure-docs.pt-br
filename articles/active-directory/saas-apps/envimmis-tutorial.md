---
title: 'Tutorial: Integração do Azure Active Directory ao Envi MMIS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff60e378e900d618cfc07f53959aa2d64518353c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103362"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Tutorial: Integração do Azure Active Directory ao Envi MMIS

Neste tutorial, você aprenderá a integrar o Envi MMIS ao Microsoft Azure Active Directory (Azure AD).
A Integração do Envi MMIS ao Microsoft Azure Active Directory oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Envi MMIS.
* Você pode permitir que os usuários sejam conectados automaticamente ao Envi MMIS (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com o eTouches, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Envi MMIS

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Envi MMIS dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-envi-mmis-from-the-gallery"></a>Adição do Envi MMIS da galeria

Para configurar a integração do Envi MMIS ao Microsft Azure Active Directory, você precisará adicionar o Envi MMIS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Envi MMIS por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite  **Envi MMIS**, selecione  **Envi MMIS** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Envi MMIS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Envi MMIS, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Envi MMIS.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Envi MMIS, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Envi MMIS](#configure-envi-mmis-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Envi MMIS](#create-envi-mmis-test-user)** – para ter um equivalente de Brenda Fernandes no Envi MMIS que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Envi MMIS, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Envi MMIS**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar Logon Único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do Envi MMIS](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Envi MMIS](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com [equipe de suporte ao Cliente do Envi MMIS](mailto:support@ioscorp.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de autenticação SAML**, clique em **Baixar** para baixar o **XML de metadados de federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o Envi MMIS**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-envi-mmis-single-sign-on"></a>Configurar o logon único do Envi MMIS

1. Em outra janela do navegador da Web, entre em seu site do Envi MMIS como administrador.

2. Clique na guia **Meu Domínio**.

    ![Botão Salvar em Configurar Logon Único](./media/envimmis-tutorial/configure1.png)

3. Clique em **Editar**.

    ![Botão Salvar em Configurar Logon Único](./media/envimmis-tutorial/configure2.png)

4. Selecione a caixa de seleção **Usar autenticação remota** e, em seguida, selecione **Redirecionamento HTTP** da lista suspensa **Tipo de Autenticação**.

    ![Botão Salvar em Configurar Logon Único](./media/envimmis-tutorial/configure3.png)

5. Selecione a guia **Recursos** e, em seguida, clique em **Carregar Metadados**.

    ![Botão Salvar em Configurar Logon Único](./media/envimmis-tutorial/configure4.png)

6. Na janela pop-up **Carregar Metadados**, realize as seguintes etapas:

    ![Botão Salvar em Configurar Logon Único](./media/envimmis-tutorial/configure5.png)

    a. Selecione a opção **Arquivo** na lista suspensa **Carregar Formulário**.

    b. Carregue o arquivo de metadados baixado do portal do Azure selecionando escolher **ícone do arquivo**.

    c. Clique em **OK**.

7. Após o upload do arquivo de metadados baixado, os campos serão populados automaticamente. Clique em **Atualizar**

    ![Botão Salvar em Configurar Logon Único](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@domíniodaempresa.extensão**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Envi MMIS.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Envi MMIS**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Envi MMIS**.

    ![O link do Envi MMIS na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função de usuário apropriada para o usuário na lista e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-envi-mmis-test-user"></a>Criar um usuário de teste do Envi MMIS

Para permitir que os usuários do Azure AD entrem no Envi MMIS, eles precisarão ser provisionados no Envi MMIS. No caso do Envi MMIS, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do Envi MMIS como administrador.

2. Clique na guia **Lista de Usuário**.

    ![Adicionar Funcionário](./media/envimmis-tutorial/user1.png)

3. Clique no botão **Adicionar Usuário**.

    ![Adicionar Funcionário](./media/envimmis-tutorial/user2.png)

4. Na seção **Adicionar Usuário** , realize as seguintes etapas:

    ![Adicionar Funcionário](./media/envimmis-tutorial/user3.png)

    a. Na caixa de texto **Nome de Usuário**, digite o nome de usuário da conta de Brenda Fernandes como **brendafernandes\@contoso.com**.
    
    b. Na caixa de texto **Nome**, digite o primeiro nome de Brenda Fernandes como **Britta**.

    c. Na caixa de texto **Sobrenome**, digite o primeiro nome de Brenda Fernandes como **Fernandes**.

    d. Insira o título do usuário no **título** da caixa de texto.
    
    e. Na caixa de texto **Endereço de Email**, digite o endereço de email da conta de Brenda Fernandes como **brendafernandes\@contoso.com**.

    f. Na caixa de texto **Nome de Usuário de SSO**, digite o nome de usuário da conta de Brenda Fernandes como **brendafernandes\@contoso.com**.

    g. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Envi MMIS no Painel de Acesso, você deverá ser conectado automaticamente ao Envi MMIS, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


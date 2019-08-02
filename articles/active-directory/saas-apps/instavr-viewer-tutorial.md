---
title: 'Tutorial: Integração do Azure Active Directory ao InstaVR Viewer | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o InstaVR Viewer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e005d794fa3c6c70edf3d3466b0f076e2007352
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099980"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Tutorial: Integração do Azure Active Directory ao InstaVR Viewer

Neste tutorial, você aprenderá a integrar o InstaVR Viewer ao Azure AD (Azure Active Directory).
A integração do InstaVR Viewer com o Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao InstaVR Viewer.
* Você pode permitir que seus usuários entrem automaticamente no InstaVR Viewer (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o InstaVR Viewer, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do InstaVR Viewer habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O InstaVR Viewer dá suporte ao SSO iniciado por **SP**
* O InstaVR Viewer dá suporte ao provisionamento de usuário **Just In Time**

## <a name="adding-instavr-viewer-from-the-gallery"></a>Adicionando o InstaVR Viewer da galeria

Para configurar a integração do InstaVR Viewer com Azure AD, você precisará adicionar InstaVR Viewer da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o InstaVR Viewer da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **InstaVR Viewer**, selecione **InstaVR Viewer** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![InstaVR Viewer na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o InstaVR Viewer, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do InstaVR Viewer.

Para configurar e testar o logon único do Azure AD com o InstaVR Viewer, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do InstaVR Viewer](#configure-instavr-viewer-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do InstaVR Viewer](#create-instavr-viewer-test-user)** – para ter um equivalente de Brenda Fernandes no InstaVR Viewer que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o InstaVR Viewer, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **InstaVR Viewer**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e URLs do InstaVR Viewer](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Não há nenhum padrão fixo para a URL de Logon. Ela é gerada quando o cliente do InstaVR Viewer realiza o empacotamento da Web. Ela é exclusiva para cada cliente e pacote. Para obter a URL de Logon exata, você precisa fazer logon em sua instância do InstaVR Viewer e executar o empacotamento de Web.

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize esse valor com o valor do Identificador real, que é explicado posteriormente neste tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **Certificado (Base64)** e do **Arquivo de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadata-certificatebase64.png)

6. Na seção **Configurar InstaVR Viewer**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-instavr-viewer-single-sign-on"></a>Configurar o Logon Único do InstaVR Viewer

1. Abra uma nova janela do navegador da Web e faça logon no site da empresa do InstaVR Viewer como administrador.

2. Clique no **Ícone do usuário** e selecione **Conta**.

    ![Configuração do InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Role para baixo até a **Autenticação SAML** e execute as seguintes etapas:

    ![Configuração do InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Na caixa de texto **URL de SSO**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    b. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiada do portal do Azure.

    c. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** que você copiou do portal do Azure.

    d. Para fazer upload do arquivo de Certificado baixado, clique em **Atualizar**.

    e. Para fazer upload do arquivo de Metadados de Federação baixado, clique em **Atualizar**.

    f. Copie o valor da **ID da Entidade** e cole-o na caixa de texto **Identificador (ID da Entidade)** na seção **Configuração Básica de SAML** no portal do Azure.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao InstaVR Viewer.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **InstaVR Viewer**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e escolha **InstaVR Viewer**.

    ![O link do InstaVR Viewer na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-instavr-viewer-test-user"></a>Criar um usuário de teste do InstaVR Viewer

Nesta seção, é criado um usuário chamado Brenda Fernandes no InstaVR Viewer. O InstaVR Viewer dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no InstaVR Viewer, um novo será criado após a autenticação. Se encontrar problemas, contate a [Equipe de suporte do InstaVR Viewer](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Testar logon único

1. Abra uma nova janela do navegador da Web e faça logon no site da empresa do InstaVR Viewer como administrador.

2. Selecione **Pacote** no painel de navegação à esquerda e selecione **Criar pacote para a Web**.

    ![Configuração do InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Selecione **Baixar**.

    ![Configuração do InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Selecione **Abrir Página Hospedada**; depois disso, ele será redirecionado para o Azure AD para logon.

    ![Configuração do InstaVR Viewer](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Insira suas credenciais do Azure AD para fazer logon com êxito no Azure AD por meio do SSO.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

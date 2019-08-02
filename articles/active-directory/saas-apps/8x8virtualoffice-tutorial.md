---
title: 'Tutorial: Integração do Azure Active Directory com o 8x8 Virtual Office | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o 8x8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fb9ee12c0373f2d7038417935709caae4a6eb43
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107480"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Integração do Azure Active Directory com o 8x8 Virtual Office

Neste tutorial, você aprende a integrar o 8x8 Virtual Office ao Azure AD (Azure Active Directory).
A integração do 8x8 Virtual Office ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao 8x8 Virtual Office.
* Você pode permitir que seus usuários façam logon automaticamente no 8x8 Virtual Office (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o 8x8 Virtual Office, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do 8x8 Virtual Office habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.


* O 8x8 Virtual Office é compatível com SSO iniciado por **IDP**

* O 8x8 Virtual Office é compatível com o provisionamento de usuário **Just In Time**

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Adicionando o 8x8 Virtual Office da galeria

Para configurar a integração do 8x8 Virtual Office ao Azure AD, você precisará adicionar o 8x8 Virtual Office à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o 8x8 Virtual Office por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **8x8 Virtual Office**, selecione **8x8 Virtual Office** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![8x8 Virtual Office na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o 8x8 Virtual Office com base em um usuário de teste chamado **Britta Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do 8x8 Virtual Office.

Para configurar e testar o logon único do Azure AD com o 8x8 Virtual Office, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do 8x8 Virtual Office](#configure-8x8-virtual-office-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do 8x8 Virtual Office](#create-8x8-virtual-office-test-user)** – para ter um equivalente de Brenda Fernandes no 8x8 Virtual Office que esteja vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o 8x8 Virtual Office, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **8x8 Virtual Office**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na caixa de diálogo **Configuração básica de SAML**, realize as seguintes etapas:

    ![Domínio 8x8 Virtual Office e informações de logon único de URLs](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://sso.8x8.com/saml2`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://sso.8x8.com/saml2`

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Bruto)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

6. Na seção **Configurar o 8x8 Virtual Office**, copie a URL apropriada de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-8x8-virtual-office-single-sign-on"></a>Configurar o logon único para 8x8 Virtual Office

1. Faça logon no seu locatário do 8x8 Virtual Office como administrador.

1. Selecione **Virtual Office Account Mgr** no painel do aplicativo.

    ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Selecione a **conta comercial** que deseja gerenciar e clique no botão **Entrar**.

    ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Clique em **contas** guia na lista de menus.

   ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Clique em **Single Sign On** na lista Accounts.
  
   ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Selecione **Logon único** em Métodos de autenticação e clique em **SAML**.

   ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. No **logon único do SAML** , realize as seguintes etapas:

   ![Configurar no lado do aplicativo](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

   a. Na caixa de texto **URL de login**, cole o **valor do URL de login** que você copiou do portal do Azure.

   b. No **URL de logoff** caixa de texto, cole **URL de logoff** valor que você copiou do portal do Azure.

   c. Na caixa de texto **URL do emissor**, cole o valor **Identificador do Azure AD** que você copiou do portal do Azure.

   d. Clique em **procurar** botão para carregar o certificado que você baixou do portal do Azure.

   e. Clique no botão **Salvar** .

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao 8x8 Virtual Office.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **8x8 Virtual Office**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **8x8 Virtual Office**.

    ![Link do 8x8 Virtual Office na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-8x8-virtual-office-test-user"></a>Criar um usuário de teste do 8x8 Virtual Office

Nesta seção, um usuário chamado Brenda Fernandes é criado no 8x8 Virtual Office. O 8x8 Virtual Office dá suporte ao **provisionamento do usuário Just-In-Time**, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no 8x8 Virtual Office, um novo usuário será criado após a autenticação.

> [!NOTE]
> Se precisar criar um usuário manualmente, será necessário contatar a [equipe de suporte do 8x8 Virtual Office](https://www.8x8.com/about-us/contact-us).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do 8x8 Virtual Office no Painel de Acesso, você deverá ser conectado automaticamente ao 8x8 Virtual Office no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


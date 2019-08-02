---
title: 'Tutorial: Integração do Azure Active Directory ao LearnUpon | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdcd8c6024b3cacb422b556718bbbdbb5d601c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098233"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Tutorial: Integração do Azure Active Directory ao LearnUpon

Neste tutorial, você aprenderá a integrar o LearnUpon ao Azure AD (Azure Active Directory).
A integração do LearnUpon ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao LearnUpon.
* Você pode permitir que os usuários sejam conectados automaticamente ao LearnUpon (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao LearnUpon, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do LearnUpon habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.


* O LearnUpon dá suporte ao SSO iniciado por **IDP**

* O LearnUpon dá suporte ao provisionamento de usuário **Just-In-Time**


## <a name="adding-learnupon-from-the-gallery"></a>Adicionando o LearnUpon da galeria

Para configurar a integração do LearnUpon ao AD do Azure, você precisará adicionar o LearnUpon da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LearnUpon da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **LearnUpon**, selecione **LearnUpon** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![LearnUpon na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o LearnUpon, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LearnUpon.

Para configurar e testar o logon único do AD do Azure com o LearnUpon, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do LearnUpon](#configure-learnupon-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do LearnUpon](#create-learnupon-test-user)** – para ter um equivalente de Brenda Fernandes no LearnUpon que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o LearnUpon, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **LearnUpon**, selecione **Logon Único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do LearnUpon](common/idp-reply.png)

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte ao Cliente LearnUpon](https://www.learnupon.com/features/support/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, localize a **IMPRESSÃO DIGITAL**: isso será adicionado às suas configurações SAML do LearnUpon.

    ![O link de download do Certificado](common/certificateraw.png)

6. Na seção **Configurar o LearnUpon**, copie a URL apropriada de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-learnupon-single-sign-on"></a>Configurar o logon único do LearnUpon

1. Abra outra instância do navegador e entre no LearnUpon com uma conta de administrador.

1. Clique na guia **Configurações** .

    ![Configurar o logon único](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Clique em **Logon Único - SAML** e em **Configurações Gerais** para definir configurações de SAML.
   
    ![Configurar o logon único](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Na seção **Configurações Gerais** , execute as seguintes etapas:
   
    ![Configurar o logon único](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selecione **Habilitado**.

    b. Selecione **versão** como **2.0**.

    c. Selecione **Ignorar condições** como **Não**.

    d. Na caixa de texto **Nome do parâmetro Post de Token SAML**, digite o nome do parâmetro da solicitação post para a URL de consumidor SAML indicada acima, que contém a declaração SAML a ser verificada e autenticada. Por exemplo, **SAMLResponse**.

    e. Na caixa de texto **Formato de Nome de Identificador**, digite o valor que indica onde em sua Declaração SAML o identificador de usuários (endereço de Email) reside. Por exemplo: `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.
  
    f. Na caixa de texto **Identificar o Local do Provedor**, digite o valor que indica para onde os usuários são enviados ao clicarem em seu ícone carregado na tela de logon do portal do Azure.
  
    g. Na caixa de texto **URL de Saída**, cole o valor da **URL de Logoff** copiada do portal do Azure.

    h. Clique em **Gerenciar impressões digitais**e carregue a impressão digital do certificado baixado.

1. Clique em **Configurações de Usuário**e execute as seguintes etapas:

     ![Configurar o logon único](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Na caixa de texto **Formato do Identificador de Nome**, digite o valor que indica onde na instrução de sua declaração do SAML reside o nome dos usuários. Por exemplo: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
  
    b. Na caixa de texto **Formato do Identificador de Sobrenome**, digite o valor que indica onde na instrução de sua declaração do SAML reside o sobrenome dos usuários. Por exemplo: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao LearnUpon.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **LearnUpon**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **LearnUpon**.

    ![O link do LearnUpon na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-learnupon-test-user"></a>Criar um usuário de teste do LearnUpon

Nesta seção, um usuário chamado Brenda Fernandes será criado no LearnUpon. O LearnUpon dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no LearnUpon, um novo será criado após a autenticação. Se precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça do LearnUpon no Painel de Acesso, você deverá ser conectado automaticamente ao LearnUpon para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
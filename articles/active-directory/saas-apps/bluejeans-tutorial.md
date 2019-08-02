---
title: 'Tutorial: Integração do Azure Active Directory ao BlueJeans | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0308fbb103fb06bd2dffe0a442346a3fc4f7db62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106167"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Tutorial: Integração do Azure Active Directory ao BlueJeans

Neste tutorial, você aprende a integrar o BlueJeans ao Azure AD (Azure Active Directory).
A integração do BlueJeans ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao BlueJeans.
* Você pode permitir que os usuários sejam conectados automaticamente ao BlueJeans (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao BlueJeans, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do BlueJeans

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O BlueJeans dá suporte ao SSO iniciado por **SP**

* O BlueJeans dá suporte ao [provisionamento de usuário **Automatizado**](bluejeans-provisioning-tutorial.md)

## <a name="adding-bluejeans-from-the-gallery"></a>Adicionando o BlueJeans por meio da galeria

Para configurar a integração do BlueJeans ao Azure AD, você precisa adicionar o BlueJeans à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o BlueJeans por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **BlueJeans**, selecione **BlueJeans** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![BlueJeans na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o BlueJeans, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BlueJeans.

Para configurar e testar o logon único do Azure AD com o BlueJeans, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do BlueJeans](#configure-bluejeans-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do BlueJeans](#create-bluejeans-test-user)** – para ter um equivalente de Brenda Fernandes no BlueJeans que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o BlueJeans, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **BlueJeans**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](media/bluejeans-tutorial/edit-urls-bluejeans.png)

4. Na caixa de diálogo **Configuração Básica do SAML**, insira os valores a seguir:

    ![Informações de logon único de Domínio e URLs do BlueJeans](media/bluejeans-tutorial/tutorial_bluejeans-basic-configuration.png)

   - Na caixa de texto **Identificador**, digite as seguintes informações: `http://samlsp.bluejeans.com`
    
   - Na caixa de texto **URL de logon**, digite a URL da página de destino fornecido a você pelo BlueJeans (para obter esse valor, entre em contato com a [equipe de suporte do BlueJeans Client](https://support.bluejeans.com/contact)): `https://<companyname>.bluejeans.com`
    
   - Clique em **Save** (Salvar).

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o BlueJeans**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-bluejeans-single-sign-on"></a>Configurar o Logon Único do BlueJeans

1. Em outra janela do navegador da Web, entre em seu site de empresa do **BlueJeans** como administrador.

2. Acesse **ADMINISTRADOR \> CONFIGURAÇÕES DE GRUPO \> SEGURANÇA**.

    ![Admin](./media/bluejeans-tutorial/ic785868.png "Admin")

3. Na seção **SEGURANÇA**, execute as seguintes etapas:

    ![Logon Único do SAML](./media/bluejeans-tutorial/ic785869.png "Logon Único do SAML")

    a. Selecione **Logon Único do SAML**.

    b. Selecione **Habilitar provisionamento automático**.

4. Siga em frente com as seguintes etapas:

    ![Caminho de Certificado](./media/bluejeans-tutorial/ic785870.png "Caminho de Certificado")

    a. Clique em **Escolher um Arquivo** para carregar o certificado codificado em base 64 que você baixou do portal do Azure.

    b. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **URL de Alteração de Senha**, cole o valor da **URL de Alteração de Senha** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

5. Siga em frente com as seguintes etapas:

    ![Salvar Alterações](./media/bluejeans-tutorial/ic785874.png "Salvar Alterações")

    a. Na caixa de texto **ID de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Clique em **SALVAR ALTERAÇÕES**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon\@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao BlueJeans.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **BlueJeans**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **BlueJeans**.

    ![O link do BlueJeans na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-bluejeans-test-user"></a>Criar um usuário de teste do BlueJeans

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no BlueJeans. O BlueJeans dá suporte ao provisionamento automático de usuário, que é habilitado por padrão. Você pode encontrar [aqui](bluejeans-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Entre em seu site de empresa do **BlueJeans** como administrador.

2. Acesse **ADMINISTRADOR \> GERENCIAR USUÁRIOS \> ADICIONAR USUÁRIO**.

    ![Admin](./media/bluejeans-tutorial/ic785877.png "Admin")

    > [!IMPORTANT]
    > A guia **ADICIONAR USUÁRIO** só estará disponível se, na **guia SEGURANÇA**, a opção **Habilitar provisionamento automático** estiver desmarcada.

3. Na seção **ADICIONAR USUÁRIO**, execute as etapas a seguir:

    ![Adicionar Usuário](./media/bluejeans-tutorial/ic785886.png "Adicionar Usuário")

    a. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    b. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

    c. Na caixa de texto **Escolher um Nome de Usuário do BlueJeans**, insira o nome de usuário, como **Brendafernandes**

    d. Na caixa de texto **Criar uma Senha**, insira sua senha.

    e. Na caixa de texto **Empresa**, insira a sua Empresa.

    f. Na caixa de texto **Endereço de Email**, insira o email do usuário como `brittasimon\@contoso.com`.

    g. Na caixa de texto **Criar uma ID de Reunião do BlueJeans**, insira sua ID de reunião.

    h. Na caixa de texto **Escolher uma Senha de Moderador**, insira a sua senha.

    i. Clique em **CONTINUAR**.

    ![Adicionar Usuário](./media/bluejeans-tutorial/ic785887.png "Adicionar Usuário")

    J. Clique em **ADICIONAR USUÁRIO**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do BlueJeans ou as APIs fornecidas pelo BlueJeans para provisionar as contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do BlueJeans no Painel de Acesso, você deverá ser conectado automaticamente ao BlueJeans, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Tutorial: Integração do Azure Active Directory ao Pingboard | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 341d8dd712b858572ec5df76b176258ca87c8857
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094439"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Tutorial: Integração do Azure Active Directory ao Pingboard

Neste tutorial, você aprende a integrar o Pingboard ao Azure AD (Azure Active Directory).
A integração do Pingboard ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Pingboard.
* Você pode permitir que seus usuários entrem automaticamente no Pingboard (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Pingboard, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Pingboard habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Pingboard é compatível com SSO iniciado por **SP** e **IDP**

* O Pingboard é compatível com o [provisionamento automatizado de usuários](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial) 

## <a name="adding-pingboard-from-the-gallery"></a>Adicionar o Pingboard por meio da galeria

Para configurar a integração do Pingboard ao Azure AD, você precisa adicionar o Pingboard à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Pingboard por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Pingboard**, selecione **Pingboard** do painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Pingboard na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Pingboard, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Pingboard.

Para configurar e testar o logon único do Azure AD com o Pingboard, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Pingboard](#configure-pingboard-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Pingboard](#create-pingboard-test-user)** – para ter um equivalente de Brenda Fernandes no Pingboard que esteja vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Pingboard, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Pingboard**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Pingboard](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `http://app.pingboard.com/sp`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<entity-id>.pingboard.com/auth/saml/consume`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único em Domínio e URLs do Pingboard](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte do Pingboard](https://support.pingboard.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o Pingboard**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-pingboard-single-sign-on"></a>Configurar o logon único do Pingboard

1. Para configurar o SSO no lado do Pingboard, abra uma nova janela do navegador e entre em sua conta do Pingboard. Você deve ser administrador do Pingboard para configurar o logon único.

2. No menu superior, selecione **Aplicativos > Integrações**

    ![Configurar o logon único](./media/pingboard-tutorial/Pingboard_integration.png)

3. Na página **Integrações**, localize o bloco **"Azure Active Directory"** e clique nele.

    ![Integração de logon único do Pingboard](./media/pingboard-tutorial/Pingboard_aad.png)

4. No modal que vem a seguir, clique em **"Configurar"**

    ![Botão de configuração do Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

5. Na página seguinte, você notará que a "Integração SSO do Azure está habilitada". Abra o arquivo Metadados XML baixado em um bloco de notas e cole o conteúdo em **Metadados IDP**.

    ![Tela de configuração de SSO do Pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. O arquivo está validado, e se tudo estiver correto, o logon único será habilitado.

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

Nesta seção, você habilita o Brenda Fernandes para usar o logon único do Azure, concedendo acesso ao Pingboard.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Pingboard**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Pingboard**.

    ![Link do Pingboard na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-pingboard-test-user"></a>Criar um usuário de teste do Pingboard

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Pingboard. O Pingboard dá suporte ao provisionamento automático de usuário, que é habilitado por padrão. Você pode encontrar [aqui](pingboard-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Entre em seu site de empresa do Pingboard como administrador.

2. Clique no botão **"Adicionar Funcionário"** na página **Diretório**.

    ![Adicionar Funcionário](./media/pingboard-tutorial/create_testuser_add.png)

3. Na página de diálogo **“Adicionar Funcionário”** execute as seguintes etapas:

    ![Convidar Pessoas](./media/pingboard-tutorial/create_testuser_name.png)

    a. Na caixa de texto **Nome Completo Name**, digite o nome completo do usuário, como **Brenda Fernandes**.

    b. Na caixa de texto **Email**, digite o endereço de email do usuário, como **brittasimon@contoso.com** .

    c. Na caixa de texto **Cargo**, digite o cargo de Brenda Fernandes.

    d. Na lista suspensa **Local**, selecione o local de Brenda Fernandes.

    e. Clique em **Adicionar**.

4. Uma tela de confirmação será exibida para confirmar a adição do usuário.

    ![confirmar](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Pingboard no Painel de Acesso, você deverá ser conectado automaticamente ao Pingboard com SSO configurado. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar Provisionamento de Usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)

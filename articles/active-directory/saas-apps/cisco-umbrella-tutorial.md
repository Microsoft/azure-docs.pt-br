---
title: 'Tutorial: Integração do Azure Active Directory ao Cisco Umbrella | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Umbrella.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 255af92dfecdf891568d7d57c17512bb178a51e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105572"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Tutorial: Integração do Azure Active Directory ao Cisco Umbrella

Neste tutorial, você aprenderá a integrar o Cisco Umbrella com o Azure Active Directory (Azure AD).
A integração do Cisco Umbrella com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Cisco Umbrella.
* Você pode permitir que seus usuários entrem automaticamente no Cisco Umbrella (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Cisco Umbrella, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Cisco Umbrella habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Cisco Umbrella tem suporte para SSO iniciado por **SP e IDP**

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Adicionando o Cisco Umbrella da galeria

Para configurar a integração do Cisco Umbrella com o Azure AD, você precisa adicionar o Cisco Umbrella da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco Umbrella da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cisco Umbrella**, selecione **Cisco Umbrella** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Cisco Umbrella na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o [nome do aplicativo], com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do [nome do aplicativo].

Para configurar e testar o logon único do Azure AD com o [nome do aplicativo], você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Cisco Umbrella](#configure-cisco-umbrella-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Cisco Umbrella](#create-cisco-umbrella-test-user)** – para ter um equivalente de Brenda Fernandes no Cisco Umbrella vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do AD do Azure com o [nome do aplicativo], execute as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cisco Umbrella**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Informações de logon único em domínio e URLs do Cisco Umbrella](common/both-preintegrated-signon.png)

    a. Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    b. Clique em **Definir URLs adicionais**.

    c. Na caixa de texto **URL de Logon**, digite uma URL: `https://login.umbrella.com/sso`

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **XML de metadados** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar Cisco Umbrella**, copie a URL apropriada de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-cisco-umbrella-single-sign-on"></a>Configurar o logon único do Cisco Umbrella

1. Em outra janela do navegador, entre em seu site da empresa do Cisco Umbrella como administrador.

2. No lado esquerdo do menu, clique em **Admin**, navegue até **Autenticação** e, em seguida, clique em **SAML**.

    ![O administrador](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Escolha **Outros** e clique em **AVANÇAR**.

    ![O outro](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Na página de **Metadados do Cisco Umbrella**, clique em **AVANÇAR**.

    ![Os metadados](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Na guia **Carregar Metadados**, se você tiver configurado o SAML previamente, selecione a opção **Clique aqui para alterá-los** e siga as próximas etapas.

    ![Avançar](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. Na **Opção A: Carregar arquivo XML**, carregue o arquivo **XML de Metadados de Federação** que você baixou do portal do Azure. Depois de carregar os metadados, os valores abaixo são populados automaticamente; em seguida, clique em **Avançar**.

    ![Escolher o arquivo](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. Na seção **Validar Configuração do SAML**, clique em **TESTE SUA CONFIGURAÇÃO DE SAML**.

    ![O teste](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Clique em **SALVAR**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Cisco Umbrella.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Cisco Umbrella**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Cisco Umbrella**.

    ![O link do Cisco Umbrella na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-cisco-umbrella-test-user"></a>Criar um usuário de teste do Cisco Umbrella

Para permitir que os usuários do Azure AD façam logon no Cisco Umbrella, eles devem ser provisionados no Cisco Umbrella.  
No caso do Cisco Umbrella, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Em outra janela do navegador, entre em seu site da empresa do Cisco Umbrella como administrador.

2. No lado esquerdo do menu, clique em **Admin** e navegue até **Contas**.

    ![A conta](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Na página **Contas**, clique em **Adicionar** no lado superior direito da página e execute as seguintes etapas.

    ![O usuário](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. No campo **Nome**, insira o nome, como **Brenda**.

    b. No campo **Sobrenome**, insira o sobrenome, como **Fernandes**.

    c. Em **Escolher Função de Administrador Delegado**, selecione sua função.
  
    d. No campo **Endereço de Email**, digite o endereço de email do usuário, como **brendafernandes\@contoso.com**.

    e. No campo **Senha**, insira sua senha.

    f. No campo **Confirmar Senha**, insira a senha novamente.

    g. Clique em **CRIAR**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Cisco Umbrella no Painel de Acesso, você deverá ser conectado automaticamente ao Cisco Umbrella no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

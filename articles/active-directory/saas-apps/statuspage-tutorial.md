---
title: 'Tutorial: Integração do Azure Active Directory ao StatusPage | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: d947f610e6a753ce2ed349917640b07a55bbb735
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089869"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutorial: Integração do Azure Active Directory ao StatusPage

Neste tutorial, você aprende a integrar o StatusPage ao Azure AD (Azure Active Directory).
A integração do StatusPage ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao StatusPage.
* Você pode permitir que os usuários sejam conectados automaticamente ao StatusPage (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o StatusPage, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do StatusPage

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O StatusPage dá suporte ao SSO iniciado por **IdP**

## <a name="adding-statuspage-from-the-gallery"></a>Adicionando o StatusPage da galeria

Para configurar a integração do StatusPage ao AD do Azure, você precisará adicionar o StatusPage da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o StatusPage da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **StatusPage**, selecione **StatusPage** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![StatusPage na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o StatusPage, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do StatusPage.

Para configurar e testar o logon único do AD do Azure com o StatusPage, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do StatusPage](#configure-statuspage-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do StatusPage](#create-statuspage-test-user)** – para ter um equivalente de Brenda Fernandes no StatusPage que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o StatusPage, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **StatusPage**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do StatusPage](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Entre em contato com a equipe de suporte do StatusPage em [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)para solicitar os metadados necessários para a configuração do logon único. 
    >
    > a. Nos metadados, copie o valor de Issuer e cole-o na caixa de texto **Identificador** .
    >
    > b. Nos metadados, copie o valor a URL de Resposta e cole-a na caixa de texto **URL de Resposta** .

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o StatusPage**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-statuspage-single-sign-on"></a>Configurar o logon único do StatusPage

1. Em outra janela do navegador, entre em seu site de empresa do StatusPage como administrador.

1. Na barra de ferramentas principal, clique em **Gerenciar Conta**.

    ![Configurar o logon único](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Clique na guia **Logon Único** .

    ![Configurar o logon único](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na página Instalação do SSO, execute as seguintes etapas:

    ![Configurar o logon único](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Configurar o logon único](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Na caixa de texto **URL de Destino de SSO**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado** .

    c. Clique em **SALVAR CONFIGURAÇÃO**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao StatusPage.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **StatusPage**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **StatusPage**.

    ![O link do StatusPage na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-statuspage-test-user"></a>Criar um usuário de teste do StatusPage

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no StatusPage.

O StatusPage dá suporte ao provisionamento de usuário just-in-time. Você já habilitou isso em [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on).

**Para criar um usuário chamado Brenda Fernandes no StatusPage, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do StatusPage como administrador.

1. Na parte superior do menu, clique em **Gerenciar Conta**.

    ![Configurar o logon único](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Clique na guia **Membros da Equipe**.
  
    ![Criação de um usuário de teste do AD do Azure](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Clique em **ADICIONAR MEMBRO DA EQUIPE**.
  
    ![Criação de um usuário de teste do AD do Azure](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Digite o **Endereço de Email**, o **Nome** e o **Sobrenome** de um usuário válido que você deseja provisionar nas caixas de texto relacionadas. 

    ![Criação de um usuário de teste do AD do Azure](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Como **Função**, escolha **Administrador do Cliente**.

1. Clique em **CRIAR CONTA**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do StatusPage no Painel de Acesso, você deverá ser conectado automaticamente ao StatusPage, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

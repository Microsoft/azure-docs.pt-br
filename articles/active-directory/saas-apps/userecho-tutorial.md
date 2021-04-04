---
title: 'Tutorial: integração do Azure Active Directory ao UserEcho | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o UserEcho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: be52d220848a27c1307e71fdb7cc4dccfaeb9618
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92509311"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Tutorial: Integração do Active Directory do Azure com o UserEcho

Neste tutorial, você aprenderá como integrar o UserEcho ao Azure AD (Azure Active Directory).
A integração do UserEcho ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao UserEcho.
* Você pode permitir que os usuários sejam conectados automaticamente ao UserEcho (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do AD do Azure ao UserEcho, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do UserEcho habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O UserEcho dá suporte ao SSO iniciado por **SP**

## <a name="adding-userecho-from-the-gallery"></a>Adicionando o UserEcho da galeria

Para configurar a integração do UserEcho ao AD do Azure, você precisará adicionar o UserEcho da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o UserEcho da galeria, execute as etapas a seguir:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **UserEcho**, selecione **UserEcho** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![UserEcho na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o UserEcho com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do UserEcho.

Para configurar e testar o logon único do AD do Azure com o UserEcho, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do UserEcho](#configure-userecho-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do UserEcho](#create-userecho-test-user)** – para ter um equivalente de Brenda Fernandes no UserEcho que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o UserEcho, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **UserEcho**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do UserEcho](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.userecho.com/`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do UserEcho](https://feedback.userecho.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o UserEcho**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-userecho-single-sign-on"></a>Configurar o logon único do UserEcho

1. Em outra janela do navegador, entre em seu site de empresa do UserEcho como administrador.

2. Na barra de ferramentas na parte superior, clique em seu nome de usuário para expandir o menu e clique em **Instalação**.
   
    ![A captura de tela mostra a opção Configuração selecionada no site do UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Clique em **Integrações**.
   
    ![A captura de tela mostra a opção Integrações selecionada no menu Configurações.](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Clique em **site** e em **Logon único (SAML2)** .
   
    ![A captura de tela mostra a opção SAML2 de Logon Único selecionada no menu Integrações.](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Na página **Logon Único (SAML)** , execute as seguintes etapas:
   
    ![A captura de tela mostra a página do SAML de Logon Único, na qual você pode inserir os valores descritos.](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Para **Habilitado para SAML**, selecione **Sim**.
    
    b. Cole a **URL de Logon**, copiada do portal do Azure, na caixa de texto **URL de SSO SAML**.
    
    c. Cole a **URL de Logoff**, copiada do portal do Azure, na caixa de texto **URL de Logoff Remoto**.
    
    d. Abra o certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado X.509** .
    
    e. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao UserEcho.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **UserEcho**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **UserEcho**.

    ![O link do UserEcho na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-userecho-test-user"></a>Criar usuário de teste do UserEcho

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no UserEcho.

**Para criar um usuário chamado Brenda Fernandes no UserEcho, execute as seguintes etapas:**

1. Faça logon no site da empresa do UserEcho como um administrador.

2. Na barra de ferramentas na parte superior, clique em seu nome de usuário para expandir o menu e clique em **Instalação**.
   
    ![A captura de tela mostra a opção Configuração selecionada no site do UserEcho.](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Clique em **Usuários** para expandir a seção **Usuários**.
   
    ![A captura de tela mostra a opção Usuários selecionada no menu Configurações.](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Clique em **Usuários**.
   
    ![A captura de tela mostra a guia Usuários selecionada.](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Clique em **Convidar um novo usuário**.
   
    ![A captura de tela mostra o controle Convidar um novo usuário.](./media/userecho-tutorial/tutorial_userecho_12.png)

6. Na caixa de diálogo **Convidar novo usuário** , execute as seguintes etapas:
   
    ![A captura de tela mostra a caixa de diálogo Convidar um novo usuário, em que você pode inserir as informações do usuário.](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário como Brenda Fernandes.
    
    b.  Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.
    
    c. Clique em **Convidar**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do UserEcho no Painel de Acesso, você deverá ser conectado automaticamente ao UserEcho, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
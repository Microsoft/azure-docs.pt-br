---
title: 'Tutorial: Integração do Azure Active Directory ao Yodeck | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Yodeck.
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
ms.openlocfilehash: 68e961336d7e86f9983fea49044137894ab0f985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895061"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Tutorial: Integração do Active Directory do Azure ao Yodeck

Neste tutorial, você aprenderá a integrar o Yodeck ao Azure AD (Azure Active Directory).
A integração do Yodeck ao AD do Azure oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Yodeck
* Você pode permitir que os usuários sejam conectados automaticamente ao Yodeck (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do AD do Azure com o Yodeck, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único do Yodeck

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Yodeck dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-yodeck-from-the-gallery"></a>Adição do Yodeck da galeria

Para configurar a integração do Yodeck ao Azure AD, você precisará adicionar o Yodeck da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Yodeck por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Yodeck**, selecione **Yodeck** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Yodeck na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Yodeck, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Yodeck.

Para configurar e testar o logon único do Azure AD com o Yodeck, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Yodeck](#configure-yodeck-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Yodeck](#create-yodeck-test-user)** – para ter um equivalente de Brenda Fernandes no Yodeck que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Yodeck, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Yodeck**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute a seguinte etapa:

    ![Informações de logon único de Domínio e URLs do Yodeck](common/idp-identifier.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://app.yodeck.com/api/v1/account/metadata/`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![A captura de tela mostra a Configuração Básica do SAML com o link Carregar arquivo de metadados.](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.yodeck.com/login`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Configurar o logon único do Yodeck

1. Para automatizar a configuração no **Yodeck**, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![A captura de tela mostra o botão Instalar a extensão.](./media/target-process-tutorial/install_extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o Yodeck** para ser direcionado ao aplicativo Yodeck. Nele, forneça as credenciais de administrador para entrar no Yodeck. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

    **Se desejar configurar o aplicativo manualmente, realize as seguintes etapas:**

1. Em outra janela do navegador da Web, entre em seu site de empresa do Yodeck como administrador.

1. Clique no formulário de opções **Configurações do usuário** no canto superior direito da página e selecione **Configurações da conta**.

    ![A captura de tela mostra o botão Configurações de Conta selecionado para o usuário.](./media/yodeck-tutorial/configure1.png)

1. Selecione **SAML** e execute as seguintes etapas:

    ![A captura de tela mostra a guia SAML na qual você pode executar essas etapas.](./media/yodeck-tutorial/configure2.png)

    a. Selecione **Importar da URL**.

    b. Na caixa de texto **URL**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do Portal do Azure e clique em **Importar**.
    
    c. Depois de importar a **URL de metadados de federação do aplicativo**, os campos restantes são preenchidos automaticamente.

    d. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Yodeck.

1. No portal do Azure, escolha **Aplicativos empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **Yodeck**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Yodeck**.

    ![O link do Yodeck na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-yodeck-test-user"></a>Criar usuário de teste do Yodeck

Para permitir que os usuários do Azure AD entrem no Yodeck, eles devem ser provisionados no Yodeck. No caso do Yodeck, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do Yodeck como administrador.

2. Clique no formulário de opções **Configurações do usuário** no canto superior direito da página e selecione **Usuários**.

    ![A captura de tela mostra o botão Usuários selecionado para o usuário.](./media/yodeck-tutorial/user1.png)

3. Clique em **+Usuário** para abrir a guia **Detalhes do usuário**.

    ![A captura de tela mostra o botão Usuários.](./media/yodeck-tutorial/user2.png)

4. Na página de diálogo **Detalhes do Usuário**, realize as etapas seguintes:

    ![A captura de tela mostra a guia Detalhes do Usuário na qual você pode executar essas etapas.](./media/yodeck-tutorial/user3.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o Sobrenome do usuário, como **Fernandes**.

    c. Na caixa de texto **Email**, digite o endereço de email do usuário, como brittasimon@contoso.com.

    d. Selecione a opção de **Permissões de conta** adequada de acordo com suas necessidades organizacionais.
    
    e. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Yodeck no Painel de Acesso, você deverá ser conectado automaticamente ao Yodeck no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
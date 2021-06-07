---
title: 'Tutorial: integração do Azure Active Directory com o Rollbar | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Rollbar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 2d5aedf24034c9ba5ee865dd0d2289169ea5f859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520635"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Tutorial: integração do Azure Active Directory com o Rollbar

Neste tutorial, você aprende como integrar o Rollbar ao Azure AD (Azure Active Directory).
A integração do Rollbar ao Azure AD oferece os seguintes benefícios:

* Você pode controlar, no Azure AD, quem terá acesso ao Rollbar.
* Você pode permitir que seus usuários entrem automaticamente no Rollbar (logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD com o Rollbar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do Rollbar habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Rollbar é compatível com SSO iniciado por **SP e IdP**

## <a name="adding-rollbar-from-the-gallery"></a>Adicionando o Rollbar da galeria

Para configurar a integração do Rollbar ao Azure AD, você precisará adicionar o Rollbar da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Rollbar da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Rollbar**, selecione **Rollbar** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Rollbar na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Rollbar com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Rollbar.

Para configurar e testar o logon único do Azure AD com o Rollbar, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Rollbar](#configure-rollbar-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Rollbar](#create-rollbar-test-user)** – para ter um equivalente de Brenda Fernandes no Rollbar que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Rollbar, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Rollbar**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Captura de tela que mostra a Configuração Básica de SAML, em que você pode inserir o Identificador e a URL de Resposta e selecionar Salvar.](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite a URL `https://saml.rollbar.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://rollbar.com/<accountname>/saml/sso/azure/`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Captura de tela que mostra Definir URLs adicionais, em que você pode inserir uma URL de Logon.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://rollbar.com/<accountname>/saml/login/azure/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do Rollbar](mailto:support@rollbar.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar Rollbar**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-rollbar-single-sign-on"></a>Configurar Logon Único do Rollbar

1. Em outra janela do navegador da Web, entre no site empresarial do Rollbar como um administrador.

1. Clique nas **Configurações de perfil** no canto superior direito e depois em **Configurações de nome de conta**.

    ![Captura de tela que mostra as configurações de nome de conta selecionadas em Configurações de Perfil.](./media/rollbar-tutorial/general.png)

1. Em SEGURANÇA, clique em **Provedor de Identidade**.

    ![Captura de tela que mostra o Provedor de Identidade selecionado em SEGURANÇA.](./media/rollbar-tutorial/configure1.png)

1. Na seção **Provedores de Identidade do SAML**, execute as seguintes etapas:

    ![Captura de tela que mostra o Provedor de Identidade do SAML, na qual você pode inserir os valores descritos.](./media/rollbar-tutorial/configure2.png)

    a. Selecione **AZURE** na lista suspensa **Provedor de Identidade do SAML**.

    b. Abra seu arquivo de metadados no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Metadados do SAML**.

    c. Clique em **Save** (Salvar).

1. Depois de clicar no botão Salvar, a tela ficará assim:

    ![Captura de tela que mostra os resultados na página Provedor de Identidade do SAML.](./media/rollbar-tutorial/configure3.png)

    > [!NOTE]
    > Para concluir a etapa a seguir, você deve primeiro se adicionar como um usuário ao aplicativo Rollbar no Azure.
    >

    a. Se deseja exigir que todos os usuários sejam autenticados por meio do Azure, clique em **Fazer logon por meio de seu provedor de identidade** para autenticar novamente por meio do Azure.  

    b.  Quando você retornar à tela, selecione a caixa de seleção **Exigir logon por meio do Provedor de Identidade do SAML**.

    b. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-a acesso ao Rollbar.

1. No portal do Azure, escolha **Aplicativos Empresariais**, escolha **Todos os aplicativos** e, em seguida, selecione **Rollbar**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Rollbar**.

    ![O link do Rollbar na Lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-rollbar-test-user"></a>Criar um usuário de teste do Rollbar

Para permitir que os usuários do Azure AD entre no Rollbar, eles devem ser provisionados no Rollbar. No caso do Rollbar, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site empresarial do Rollbar como administrador.

1. Clique nas **Configurações de perfil** no canto superior direito e depois em **Configurações de nome de conta**.

    ![Usuário](./media/rollbar-tutorial/general.png)

1. Clique em **Usuários**.

    ![Adicionar Funcionário](./media/rollbar-tutorial/user1.png)

1. Clique em **Convidar Membros da Equipe**.

    ![Captura de tela que mostra a opção Convidar Membros da Equipe selecionada.](./media/rollbar-tutorial/user2.png)

1. Na caixa de texto, digite o nome do usuário, como **brendafernandes\@contoso.com** e clique em **Adicionar/Convidar**.

    ![Captura de tela que mostra Adicionar/Convidar Membros com um endereço fornecido.](./media/rollbar-tutorial/user3.png)

1. O usuário recebe um convite e, depois de aceitá-lo, ele é criado no sistema.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Rollbar no Painel de Acesso, você deverá ser conectado automaticamente ao Rollbar no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
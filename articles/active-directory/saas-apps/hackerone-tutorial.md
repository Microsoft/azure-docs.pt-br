---
title: 'Tutorial: Integração do Azure Active Directory ao HackerOne | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HackerOne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: ac8bdc37bc2572b580deebc35931e324ed107d87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92446524"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Tutorial: Integração do Azure Active Directory ao HackerOne

Neste tutorial, você aprenderá a integrar o HackerOne ao Azure AD (Azure Active Directory).
A integração do HackerOne ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao HackerOne.
* Você pode permitir que os usuários sejam conectados automaticamente ao HackerOne (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao HackerOne, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do HackerOne

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O HackerOne dá suporte ao SSO iniciado por **SP**
* O HackerOne dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-hackerone-from-the-gallery"></a>Adicionando o HackerOne da galeria

Para configurar a integração do HackerOne ao Azure AD, você precisará adicionar o HackerOne da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HackerOne da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **HackerOne**, selecione **HackerOne** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![HackerOne na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o HackerOne, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HackerOne.

Para configurar e testar o logon único do Azure AD com o HackerOne, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do HackerOne](#configure-hackerone-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do HackerOne](#create-hackerone-test-user)** – para ter um equivalente de Brenda Fernandes no HackerOne que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o HackerOne, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **HackerOne**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do HackerOne](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, insira o seguinte: `https://hackerone.com/users/saml/sign_in?email=<configured domain>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , insira o seguinte: `hackerone.com`

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o HackerOne**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-hackerone-single-sign-on"></a>Configurar o logon único do HackerOne

1. Faça logon no seu locatário do HackerOne como administrador.

2. No menu na parte superior, clique em **Configurações**.

    ![Captura de tela que mostra a opção "Configurações" selecionada no menu.](./media/hackerone-tutorial/tutorial_hackerone_001.png)

3. Navegue para **Autenticação** e clique em **Adicionar configurações do SAML**.

    ![Captura de tela que mostra a página "Configurações de Autenticação" com o botão "Adicionar configurações do SAML" selecionado.](./media/hackerone-tutorial/tutorial_hackerone_003.png)

4. Na caixa de diálogo **Configurações de SAML** , execute as seguintes etapas:

    ![Configurar o logon único](./media/hackerone-tutorial/tutorial_hackerone_004.png)

    a. Na caixa de texto **Domínio de Email** , digite um domínio registrado.

    b. Na caixa de texto **URL de Logon Único**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    c. Abra o **Arquivo de certificado** baixado do portal do Azure no Bloco de notas, copie o conteúdo dele para a área de transferência e, em seguida, cole-o na caixa de texto **Certificado X509**.

    d. Clique em **Save** (Salvar).

5. No diálogo Configurações de Autenticação, execute as seguintes etapas:

    ![Captura de tela que mostra a caixa de diálogo "Configurações de Autenticação" com o botão "Executar teste" selecionado.](./media/hackerone-tutorial/tutorial_hackerone_005.png)

    a. Clique em **Executar teste**.

6. Quando o teste for concluído com êxito e o campo **Status** mostrar **Status do último teste: êxito**, selecione o botão **Verificação de Solicitação** para enviá-lo ao HackerOne para aprovação.

    ![Enviar ao HackerOne para aprovação](./media/hackerone-tutorial/tutorial-hackerone-006.png)

7. Depois que o HackerOne aprovar as configurações, você poderá selecionar o botão **Migrar Usuários** para exigir a autenticação de SSO para todos os usuários.

    ![Habilitar o SAML](./media/hackerone-tutorial/tutorial-hackerone-007.png)

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao HackerOne.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **HackerOne**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **HackerOne**.

    ![O link do HackerOne na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-hackerone-test-user"></a>Criar um usuário de teste do HackerOne

Nesta seção, um usuário chamado Brenda Fernandes será criado no HackerOne. O HackerOne dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no HackerOne, um novo será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do HackerOne no Painel de Acesso, você deverá ser conectado automaticamente ao HackerOne, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
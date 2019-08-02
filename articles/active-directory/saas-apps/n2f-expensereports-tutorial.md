---
title: 'Tutorial: integração do Azure Active Directory ao N2F – Relatórios de despesas | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o N2F - Expense reports.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 0a7b38f26261625c6db8acb6653b3cd9353fdcc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096511"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Tutorial: integração do Azure Active Directory ao N2F – Relatórios de despesas

Neste tutorial, você aprende a integrar o N2F - Expense reports ao Microsoft Azure Active Directory.
A integração do N2F - Expense reports ao Microsoft Azure Active Directory oferece os seguintes benefícios:

* No Microsoft Azure Active Directory, é possível controlar quem tem acesso ao N2F - Expense reports.
* Você pode permitir que os usuários sejam conectados automaticamente ao N2F – Expense reports (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao N2F - Expense reports, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do N2F – Expense reports

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O N2F – Expense reports dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Adicionando N2F - Expense reports a partir da Galeria

Para configurar a integração do N2F - Expense reports ao Microsoft Azure Active Directory, você precisará adicionar o N2F - Expense reports da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o N2F - Expense reports da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **N2F - Expense reports**, selecione **N2F - Expense reports** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![N2F - Expense reports na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o N2F – Expense reports, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do N2F – Expense reports.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o N2F - Expense reports, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do N2F – Expense reports](#configure-n2f---expense-reports-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do N2F – Expense reports](#create-n2f---expense-reports-test-user)** – para ter um equivalente de Brenda Fernandes no N2F – Expense reports que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o N2F – Expense reports, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **N2F – Expense reports**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, se você desejar configurar o aplicativo no modo iniciado pelo **IDP**, o usuário não precisará executar nenhuma etapa, pois o aplicativo já estará pré-integrado ao Azure.

    ![Informações de logon único de Domínio e URLs do N2F - Expense reports](common/preintegrated.png)

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do N2F - Expense reports](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://www.n2f.com/app/`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

7. Na seção **Configurar o myPolicies**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Configurar o logon único do N2F – Expense reports

1. Em outra janela do navegador da Web, entre em seu site empresarial do N2F - Expense como um administrador.

2. Clique em **Configurações** e, em seguida, selecione **Configurações de Adiantamento** na lista suspensa.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecione a guia **Configurações de conta**.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure2.png)

4. Selecione **Autenticação** e, em seguida, selecione a guia **+ Adicionar um método de autenticação**.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure3.png)

5. Selecione **SAML Microsoft Office 365** como método de autenticação.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure4.png)

6. Na seção **Método de autenticação**, realize as seguintes etapas:

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/configure5.png)

    a. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    b. Na caixa de texto **URL de Metadados**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do portal do Azure.

    c. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao N2F - Expense.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **N2F – Expense reports**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **N2F - Expense reports**.

    ![Link do N2F - Expense reports na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-n2f---expense-reports-test-user"></a>Criar um usuário de teste do N2F – Expense reports

Para permitir que os usuários do Microsoft Azure Active Directory façam logon no N2F - Expense reports, eles devem ser provisionados no N2F - Expense reports. No caso do N2F - Expense reports, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do N2F - Expense reports como administrador.

2. Clique em **Configurações** e, em seguida, selecione **Configurações de Adiantamento** na lista suspensa.

    ![Adicionar usuário ao N2F - Expense](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecione a guia **Usuários** do painel de navegação à esquerda.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/user1.png)

4. Selecione a guia **+ Novo Usuário**.

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/user2.png)

5. Na seção **Usuário**, execute as seguintes etapas:

    ![Configuração do N2F - Expense reports](./media/n2f-expensereports-tutorial/user3.png)

    a. Na caixa de texto **Endereço de email**, insira o endereço de email do usuário como **brendafernandes\@contoso.com**.

    b. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    c. Na caixa de texto **Nome**, insira o nome de usuário, como **BrendaFernandes**.

    d. Escolher **Função de gerente direto (N + 1)** , e **Divisão**, de acordo com o requisito da organização.

    e. Clique em **Validar e Enviar convite**.

    > [!NOTE]
    > Se você estiver enfrentando problemas ao adicionar o usuário, entre em contato com a equipe de suporte do [N2F - Expense reports](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do N2F – Expense reports no Painel de Acesso, você deverá ser conectado automaticamente ao N2F – Expense reports, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


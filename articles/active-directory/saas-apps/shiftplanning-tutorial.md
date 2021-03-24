---
title: 'Tutorial: integração do Azure Active Directory com o Humanity | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Humanity.
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
ms.openlocfilehash: e1099f271291c6d86610237454e08e06b5fe48c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522148"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Tutorial: integração do Azure Active Directory com o Humanity

Neste tutorial, você aprenderá a integrar o Humanity ao Azure AD (Azure Active Directory).
A integração do Humanity ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Humanity.
* Você pode permitir que seus usuários entrem automaticamente no Humanity (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao Humanity, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Humanity habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Humanity é compatível com o SSO iniciado por **SP**

## <a name="adding-humanity-from-the-gallery"></a>Adicionar o Humanity da galeria

Para configurar a integração do Humanity ao Azure AD, você precisará adicionar o Humanity da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Humanity da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Humanity**, selecione **Humanity** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Humanity na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Humanity, com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Humanity.

Para configurar e testar o logon único do Azure AD com o Humanity, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Humanity](#configure-humanity-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Humanity](#create-humanity-test-user)** – para ter um equivalente de Brenda Fernandes no Humanity que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Humanity, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Humanity**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Humanity](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://company.humanity.com/includes/saml/`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://company.humanity.com/app/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente Humanity](https://www.humanity.com/support/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar Humanity**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-humanity-single-sign-on"></a>Configurar o logon único do Humanity

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do **Humanity** como um administrador.

2. No menu na parte superior, clique em **Administrador**.

    ![Administrador](./media/shiftplanning-tutorial/iC786619.png "Admin")
3. Em **Integração**, clique em **Logon Único**.

    ![Captura de tela que mostra a opção Logon Único selecionada no menu Integração.](./media/shiftplanning-tutorial/iC786620.png "Logon Único")

4. Na seção **Logon Único** , realize as seguintes etapas:

    ![Captura de tela que mostra a seção Logon Único, na qual você pode inserir os valores descritos.](./media/shiftplanning-tutorial/iC786905.png "Logon Único")

    a. Selecione **SAML Habilitado**.

    b. Selecione **Permitir Logon de Senha**.

    c. Na caixa de texto **URL do Emissor SAML**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    d. Na caixa de texto **URL de Logout Remoto**, cole o valor da **URL de Logout** copiado do portal do Azure.

    e. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509** .

    f. Clique em **Salvar Configurações**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Humanity.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Humanity**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Humanity**.

    ![O link do Humanity na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-humanity-test-user"></a>Criar um usuário de teste do Humanity

Para permitir que os usuários do Azure AD façam logon no Humanity, eles deverão ser provisionados no Humanity. No caso do Humanity, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Humanity** como administrador.

2. Clique em **Administrador**.

    ![Administrador](./media/shiftplanning-tutorial/iC786619.png "Admin")

3. Clique em **Equipe**.

    ![Equipe](./media/shiftplanning-tutorial/ic786623.png "Equipe")

4. Em **Ações**, clique em **Adicionar Funcionários**.

    ![Adicionar Funcionários](./media/shiftplanning-tutorial/iC786624.png "Adicionar Funcionários")

5. Na seção **Adicionar Funcionários** , realize as seguintes etapas:

    ![Salvar Funcionários](./media/shiftplanning-tutorial/iC786625.png "Salvar Funcionários")

    a. Nas caixas de texto correspondentes, digite o **Nome**, o **Sobrenome** e o **Email** de uma conta válida do Azure AD que você deseja provisionar.

    b. Clique em **Salvar Funcionários**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Humanity ou as APIs fornecidas pelo Humanity para provisionar as contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Humanity no Painel de Acesso, você deverá ser conectado automaticamente ao Humanity, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
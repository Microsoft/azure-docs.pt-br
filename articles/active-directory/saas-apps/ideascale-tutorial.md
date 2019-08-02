---
title: 'Tutorial: Integração do Azure Active Directory ao IdeaScale | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a312e0ca70ef3a37b43f1ffb68a974a8aedaf982
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100790"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: Integração do Azure Active Directory ao IdeaScale

Neste tutorial, você aprenderá a integrar o IdeaScale ao Azure AD (Azure Active Directory).
A integração do IdeaScale ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao IdeaScale.
* Você pode permitir que os usuários sejam conectados automaticamente ao IdeaScale (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao IdeaScale, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do IdeaScale

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O IdeaScale dá suporte ao SSO iniciado por **SP**

## <a name="adding-ideascale-from-the-gallery"></a>Adicionando IdeaScale da galeria

Para configurar a integração do IdeaScale ao Azure AD, você precisará adicioná-lo da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o IdeaScale da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **IdeaScale**, selecione **IdeaScale** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![IdeaScale na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o IdeaScale, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do IdeaScale.

Para configurar e testar o logon único do Azure AD com o IdeaScale, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do IdeaScale](#configure-ideascale-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do IdeaScale](#create-ideascale-test-user)** – para ter um equivalente de Brenda Fernandes no IdeaScale que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o IdeaScale, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **IdeaScale**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do IdeaScale](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.ideascale.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do cliente do IdeaScale](https://support.ideascale.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o IdeaScale**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-ideascale-single-sign-on"></a>Configurar o logon único do IdeaScale

1. Em outra janela do navegador da Web, faça logon no site da empresa do IdeaScale como administrador.

2. Vá para **Configurações da Comunidade**.

    ![Configurações da Comunidade](./media/ideascale-tutorial/ic790847.png "Configurações da Comunidade")

3. Vá para **Segurança \> Configurações de Logon Único**.

    ![Configurações de Logon Único](./media/ideascale-tutorial/ic790848.png "Configurações de Logon Único")

4. Para **Tipo de Logon Único**, selecione **SAML 2.0**.

    ![Tipo de Logon Único](./media/ideascale-tutorial/ic790849.png "Tipo de Logon Único")

5. No diálogo **Configurações de Logon Único** , realize as seguintes etapas:

    ![Configurações de Logon Único](./media/ideascale-tutorial/ic790850.png "Configurações de Logon Único")

    a. Na caixa de texto **ID da Entidade de IdP SAML**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    b. Abra o arquivo de metadados baixado do portal do Azure no Bloco de notas, copie o conteúdo dele e cole-o na caixa de texto **Metadados do IdP SAML**.

    c. Na caixa de texto **URL de Sucesso do Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Clique em **Salvar Alterações**.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao IdeaScale.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **IdeaScale**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **IdeaScale**.

    ![O link do IdeaScale na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-ideascale-test-user"></a>Criar um usuário de teste do IdeaScale

Para permitir que os usuários do Azure AD façam logon no IdeaScale, eles deverão ser provisionados no IdeaScale. No caso do IdeaScale, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **IdeaScale** como administrador.

2. Vá para **Configurações da Comunidade**.

    ![Configurações da Comunidade](./media/ideascale-tutorial/ic790847.png "Configurações da Comunidade")

3. Vá para **Configurações Básicas \> Gerenciamento de Membros**.

4. Clique em **Adicionar Membro**.

    ![Gerenciamento de Membros](./media/ideascale-tutorial/ic790852.png "Gerenciamento de Membros")

5. Na seção Adicionar Novo Membro, execute as seguintes etapas:

    ![Adicionar Novo Membro](./media/ideascale-tutorial/ic790853.png "Adicionar Novo Membro")

    a. Na caixa de texto **Endereços de Email**, digite o endereço de email de uma conta válida do Azure AD que deseja provisionar.

    b. Clique em **Salvar Alterações**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email com um link para confirmar a conta antes que ela se torne ativa.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do IdeaScale ou as APIs fornecidas pelo IdeaScale para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do IdeaScale no Painel de Acesso, você deverá ser conectado automaticamente ao IdeaScale, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


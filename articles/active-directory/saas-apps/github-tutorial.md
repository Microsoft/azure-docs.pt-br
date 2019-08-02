---
title: 'Tutorial: Integração do Azure Active Directory com GitHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0730dd89a61b046f73e7e39c8226e66700b3494
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101639"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Tutorial: Integração do Azure Active Directory com GitHub

Neste tutorial, você aprenderá a integrar o GitHub ao Azure AD (Azure Active Directory).
A integração do GitHub ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao GitHub.
* Você pode permitir que seus usuários entrem automaticamente no GitHub (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o GitHub, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma organização do GitHub criada na [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), que requer o [plano de cobrança do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O GitHub é compatível com SSO iniciado por **SP**

* O GitHub é compatível com o [provisionamento **automatizado** de usuários](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>Adicionar o GitHub da galeria

Para configurar a integração do GitHub ao Azure AD, você precisará adicionar o GitHub da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o GitHub por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **GitHub**, selecione **GitHub.com** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![GitHub na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o GitHub com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do GitHub.

Para configurar e testar o logon único do Azure AD com o GitHub, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do GitHub](#configure-github-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do GitHub](#create-github-test-user)** – para ter um equivalente de Brenda Fernandes no GitHub que esteja vinculado à representação dela no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o GitHub, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **GitHub**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do GitHub](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>/sso`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Aqui, sugerimos que você use o valor exclusivo de cadeia de caracteres no Identificador. Acesse a seção Administração do GitHub para recuperar esses valores.

5. Seu aplicativo GitHub espera as declarações de SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo do GitHub espera que **nameidentifier** seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o GitHub**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-github-single-sign-on"></a>Configurar Logon Único do GitHub

1. Em outra janela do navegador da Web, faça logon em seu site de organização do GitHub como administrador.

2. Navegue até **Configurações** e clique em **Segurança**

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Marque a caixa **Habilitar autenticação SAML**, revelando os campos de configuração de Logon Único. Em seguida, use o valor de URL de logon único para atualizar a URL de Logon Único na configuração do Azure AD.

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Configure os seguintes campos:

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    b. Na caixa de texto **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Abra o certificado baixado do portal do Azure no bloco de notas e cole o conteúdo na caixa de texto **Certificado Público**.

    d. Clique no ícone **Editar** para editar o **Método de Assinatura** e **Método de Resumo** do **RSA-SHA1** e **SHA1** para **RSA-SHA256** e **SHA256** conforme mostrado abaixo.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Clique em **Testar configuração de SAML** para confirmar que não há falhas de validação ou erros durante o SSO.

    ![Configurações](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Clique em **Salvar**

> [!NOTE]
> O logon único no GitHub se autentica em uma organização específica no GitHub e não substitui a autenticação do GitHub em si. Portanto, se a sessão do github.com do usuário expirou, você poderá ser solicitado a autenticar-se com a ID/senha do GitHub durante o processo de logon único.

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

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo-lhe acesso ao GitHub.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **GitHub**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **GitHub**.

    ![O link do GitHub na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-github-test-user"></a>Criar um usuário de teste do GitHub

O objetivo desta seção é criar um usuário chamado Britta Simon no GitHub. O GitHub dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar [aqui](github-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do GitHub como administrador.

2. Clique em **Pessoas**.

    ![Pessoas](./media/github-tutorial/tutorial_github_config_github_08.png "Pessoas")

3. Clique em **Convidar membro**.

    ![Convidar Usuários](./media/github-tutorial/tutorial_github_config_github_09.png "Convidar Usuários")

4. Na caixa de diálogo **Convidar membro**, execute as seguintes etapas:

    a. Na caixa de texto **Email**, digite o endereço de email da conta de Brenda Fernandes.

    ![Convidar Pessoas](./media/github-tutorial/tutorial_github_config_github_10.png "Convidar Pessoas")

    b. Clique em **Enviar Convite**.

    ![Convidar Pessoas](./media/github-tutorial/tutorial_github_config_github_11.png "Convidar Pessoas")

    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do GitHub no Painel de Acesso, você deverá ser conectado automaticamente ao GitHub no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

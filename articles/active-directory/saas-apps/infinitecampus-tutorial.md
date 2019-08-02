---
title: 'Tutorial: Integração do Azure Active Directory ao Infinite Campus | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Infinite Campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100363"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Tutorial: Integração do Azure Active Directory ao Infinite Campus

Neste tutorial, você aprenderá como integrar o Infinite Campus ao Azure AD (Azure Active Directory).
A integração do Campus Infinito ao Microsoft Azure Active Directory oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Infinite Campus.
* Você pode permitir que os usuários sejam conectados automaticamente ao Infinite Campus (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory ao Infinite Campus, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Infinite Campus
* No mínimo, você precisa ser um administrador do Azure Active Directory e ter uma Função de Segurança de Produto do Campus "SIS (Sistema de Informações de Alunos)" para concluir a configuração.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Infinite Campus dá suporte ao SSO iniciado por **SP**

## <a name="adding-infinite-campus-from-the-gallery"></a>Adicionando o infinito Campus da Galeria

Para configurar a integração do Infinite Campus no Microsoft Azure Active Directory, você precisa adicionar o Infinite Campus da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o infinito Campus da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Infinite Campus**, selecione **Infinite Campus** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Campus infinito na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Infinite Campus, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Infinite Campus.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Infinite Campus, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Infinite Campus](#configure-infinite-campus-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Infinite Campus](#create-infinite-campus-test-user)** – para ter um equivalente de Brenda Fernandes no Infinite Campus que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Infinite Campus, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Infinite Campus**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção Configuração Básica do SAML, execute as seguintes etapas (observe que o domínio variará de acordo com o Modelo de Hospedagem, mas o valor **FULLY-QUALIFIED-DOMAIN** precisa corresponder à instalação do Infinite Campus):

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Domínio Campus Infinito e informações de logon único de URLs](common/sp-identifier-reply.png)

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Configurar o logon único do Infinite Campus

1. Em outra janela do navegador da Web, entre no Infinite Campus como Administrador de segurança.

2. No lado esquerdo do menu, clique em **Administração do sistema**.

    ![O administrador](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Navegue para **Segurança do usuário** >  **Gerenciamento do SAML** > **Configuração do provedor de serviços de SSO**.

    ![O saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Na página **Configuração do Provedor de Serviços de SSO**, execute as seguintes etapas:

    ![O sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Selecione **habilitar logon único do SAML**.

    b. Edite o **Nome do Atributo Opcional** para que ele contenha o **nome**

    c. Na seção **Selecionar uma opção para recuperar os dados do servidor do IdP (Provedor de Identidade)** , selecione **URL de Metadados**, cole na caixa o valor da **URL de Metadados da Federação do Aplicativo** copiado do portal do Azure e, em seguida, clique em **Sincronizar**.

    d. Depois de clicar em **Sincronizar**, os valores são preenchidos automaticamente na página **Configuração do Provedor de Serviços de SSO**. Esses valores podem ser verificados para que correspondam aos valores vistos na etapa 4 acima.

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
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

> [!NOTE]
> Caso deseje que todos os usuários do Azure tenham acesso de logon único ao Infinite Campus e dependam do sistema de permissões internas do Infinite Campus para controlar o acesso, defina a propriedade **Atribuição de Usuário Necessária** do aplicativo como Não e ignore as etapas a seguir.

Nesta seção, você permite que Britta Simon use o logon único do Azure, concedendo acesso ao Infinite Campus.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Infinite Campus**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Infinite Campus**.

    ![O link do Infinite Campus na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-infinite-campus-test-user"></a>Criar um usuário de teste do Infinite Campus

Infinite Campus tem uma arquitetura demográfica centrada. Entre em contato com a equipe de suporte do [Infinite Campus](mailto:sales@infinitecampus.com) para adicionar os usuários à plataforma Infinite Campus.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Infinite Campus no Painel de Acesso, você deverá ser conectado automaticamente ao Infinite Campus, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

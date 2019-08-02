---
title: 'Tutorial: Integração do Azure Active Directory ao Peakon | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: b093a26848701254ad674081037c266f1fb012b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094716"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Tutorial: Integração do Azure Active Directory ao Peakon

Neste tutorial, você aprenderá como integrar o Peakon ao Azure AD (Azure Active Directory).
A integração do Peakon ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Peakon.
* É possível permitir que os usuários entrem automaticamente no Peakon (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Peakon, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Peakon habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Peakon é compatível com o SSO iniciado por **SP** e **IDP**

## <a name="adding-peakon-from-the-gallery"></a>Adicionando Peakon da galeria

Para configurar a integração do Peakon ao Azure AD, você precisa adicionar o Peakon da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Peakon da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Peakon**, selecione **Peakon** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Peakon na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Peakon, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Peakon.

Para configurar e testar o logon único do Azure AD com o Peakon, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Peakon](#configure-peakon-single-sign-on)** – para configurar as definições de Logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Peakon](#create-peakon-test-user)** – para ter um equivalente de Brenda Fernandes no Peakon que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Peakon, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Peakon**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Domínio do pico e informações de logon único de URLs](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://app.peakon.com/saml/<companyid>/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://app.peakon.com/saml/<companyid>/assert`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Domínio do pico e informações de logon único de URLs](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais, explicados mais adiante no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Bruto)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

7. Na seção **Configurar Peakon**, copie o URL apropriado de acordo com sua exigência.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-peakon-single-sign-on"></a>Configurar logon único do Peakon

1. Em uma janela diferente do navegador da Web, faça login no Peakon como administrador.

2. Na barra de menus à esquerda da página, clique em **Configuração** e navegue até **Integrações**.

    ![A configuração](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Na página **Integrações**, clique em **Single Sign-On**.

    ![O único](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Na seção **Single Sign-On**, clique em **Ativar**.

    ![Permitir](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. Na seção **Single sign-on para funcionários que usam o SAML**, execute as seguintes etapas:

    ![O saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Na caixa de texto **SSO Login URL**, cole o valor de **Login URL**, copiado do portal do Azure.

    b. Na caixa de texto **SSO Logout URL**, cole o valor de **URL de logoff**, copiado do portal do Azure.

    c. Clique em **Escolha o arquivo** para carregar o certificado que você baixou do portal do Azure na caixa Certificado.

    d. Clique no **ícone** para copiar a **ID da Entidade** e cole na caixa de texto **Identificador** na seção **Configuração SAML Básica** no portal do Azure.

    e. Clique no **ícone** para copiar a **URL de Resposta (ACS)** e cole na caixa de texto **URL de Resposta** na seção **Configuração SAML Básica** no portal do Azure.

    f. Clique em **Salvar**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você deve habilitar Britta Simon usar logon único do Azure, concedendo acesso ao Peakon.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Peakon**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Peakon**.

    ![O link do Peakon na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-peakon-test-user"></a>Criar usuário de teste Peakon

Para permitir que usuários do Azure AD entrem no Peakon, eles devem ser provisionados no Peakon.  
No caso do Peakon, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no seu site da empresa Peakon como administrador.

2. Na barra de menus à esquerda da página, clique em **Configuração** e navegue até **Funcionários**.

    ![O funcionário](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. No canto superior direito da página, clique em **Adicionar funcionário**.

      ![Adicionar funcionário](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na página de diálogo **Novo funcionário**, execute as seguintes etapas:

     ![O novo funcionário](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. No **nome** caixa de texto, digite o nome como **Brenda** e o sobrenome como **simon**.

    b. Na caixa de texto **Email**, digite o endereço de email como **Brendafernandes\@contoso.com**.

    c. Clique em **criar funcionário**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Peakon no Painel de Acesso, você deverá entrar automaticamente no Zoho no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


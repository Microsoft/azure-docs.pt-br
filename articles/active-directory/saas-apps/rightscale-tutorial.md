---
title: 'Tutorial: Integração do Azure Active Directory com o Rightscale | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 53799b62da043b7680f010e1eaaf0d9243f07dd5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093066"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Tutorial: Integração do Azure Active Directory com o Rightscale

Neste tutorial, você aprende a integrar o Rightscale ao Azure AD (Azure Active Directory).
A integração do Rightscale ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Rightscale.
* Você pode habilitar seus usuários para serem automaticamente conectados no Rightscale (Logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Rightscale, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Rightscale

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Rightscale é compatível com o SSO iniciado por **SP e IDP**

## <a name="adding-rightscale-from-the-gallery"></a>Adicionando o Rightscale por meio da galeria

Para configurar a integração do Rightscale ao Azure AD, é necessário adicionar o Rightscale à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Rightscale por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Rightscale**, selecione **Rightscale** no painel de resultados, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![O Rightscale na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Rightscale, com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Rightscale precisa ser estabelecida.

Para configurar e testar o logon único do Azure AD com o Rightscale, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon único do Rightscale](#configure-rightscale-single-sign-on)**  – para definir as configurações de Logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Rightscale](#create-rightscale-test-user)**  – para ter um equivalente de Brenda Fernandes no Rightscale que esteja vinculado à declaração do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Rightscale, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Rightscale**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Informações de logon único de URLs e domínio do Rightscale](common/preintegrated.png)

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de URLs e domínio do Rightscale](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://login.rightscale.com/`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Rightscale**, copie as URLs apropriadas, de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-rightscale-single-sign-on"></a>Configurar o logon único do Rightscale

1. Para configurar o SSO para o aplicativo, você precisa entrar no locatário do RightScale como administrador.

2. No menu na parte superior, clique na guia **Configurações** e selecione **Logon Único**.

    ![Configurar o logon único](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. Clique no botão **Novo** para adicionar **Seus provedores de identidade SAML**.

    ![Configurar o logon único](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. Na caixa de texto do **Nome de Exibição**, insira o nome da sua empresa.

    ![Configurar o logon único](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. Selecione **Permitir o SSO iniciado pelo RightScale usando uma dica de descoberta** e insira seu **nome de domínio** na caixa de texto abaixo.

    ![Configurar o logon único](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Cole o valor da **URL de logon** que você copiou do portal do Azure no **Ponto de extremidade do SSO SAML** no RightScale.

    ![Configurar o logon único](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Cole o valor do **Identificador do Azure AD** que você copiou do portal do Azure no **EntityID SAML** no RightScale.

    ![Configurar o logon único](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. Clique no botão **Navegador** para carregar o certificado baixado no portal do Azure.


    ![Configurar o logon único](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. Clique em **Save** (Salvar).

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Rightscale.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**, em seguida, selecione **Rightscale**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Rightscale**.

    ![O link do Rightscale na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-rightscale-test-user"></a>Criar um usuário de teste do Rightscale

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Rightscale. Trabalhe com a [equipe de suporte do cliente ao do Rightscale](mailto:support@rightscale.com) para adicionar os usuários à plataforma Rightscale. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco do Rightscale no Painel de Acesso, você deve ser conectado automaticamente ao Rightscale para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

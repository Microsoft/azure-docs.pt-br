---
title: 'Tutorial: Integração do Azure Active Directory ao Perception United States (não UltiPro) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Perception United States (não UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e9ba42f780c93486409077383750d0635637e99b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094845"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Tutorial: Integração do Azure Active Directory ao Perception United States (não UltiPro)

Neste tutorial, você aprenderá a integrar o Perception United States (não UltiPro) ao Azure Active Directory (Azure AD).
A integração do Perception United States (não UltiPro) ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Perception United States (não UltiPro).
* Você pode permitir que seus usuários entrem automaticamente no Perception United States (não UltiPro) (Logon Único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Perception United States (não UltiPro), você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Perception United States (não UltiPro)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Perception United States (não UltiPro) é compatível com SSO iniciado por **IDP**

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Adicionar Perception United States (não UltiPro) pela galeria

Para configurar a integração do Perception United States (não UltiPro) ao Azure AD, você precisará adicionar o Perception United States (não UltiPro) pela galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Perception United States (não UltiPro) pela galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Perception United States (não UltiPro)** , selecione **Perception United States (não UltiPro)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Perception United States (não UltiPro) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Perception United States (não UltiPro) com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Perception United States (não UltiPro).

Para configurar e testar o logon único do Azure AD com o Perception United States (não UltiPro), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Perception United States (não UltiPro)](#configure-perception-united-states-non-ultipro-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Perception United States (não UltiPro)](#create-perception-united-states-non-ultipro-test-user)** – para ter um equivalente de Brenda Fernandes no Perception United States (não UltiPro) que esteja vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Perception United States (não UltiPro), execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Perception United States (não UltiPro)** , clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    ![Informações sobre logon único de domínio e URLs do Perception United States (não UltiPro)](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `https://perception.kanjoya.com/sp`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. O aplicativo **Perception United States (não UltiPro)** exige o valor do **Identificador do Azure AD** como <entity_id>, que será obtido na seção **Configurar o Perception United States (não UltiPro)** , seja codificado no URI. Para obter o valor codificado no URI, use o seguinte link: **http://www.url-encode-decode.com/** .

    d. Depois de obter o valor codificado em URI, combine-o com a **URL de resposta** conforme mencionado abaixo -

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Cole o valor acima na caixa de texto **URL de Resposta**.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Perception United States (não UltiPro)** , copie as URLs corretas de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Configurar o logon único do Perception United States (não UltiPro)

1. Em outra janela do navegador, entre no seu site empresarial em Perception United States (não UltiPro) como administrador.

2. Na barra de ferramentas principal, clique em **Configurações de conta**.

    ![Usuário do Perception United States (não UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Na página **Configurações de conta**, execute as seguintes etapas:

    ![Usuário do Perception United States (não UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Na caixa de texto **Nome da empresa**, digite o nome da **Empresa**.
    
    b. Na caixa de texto **Nome da conta**, digite o nome da **Conta**.

    c. Na caixa de texto **Email de resposta padrão**, digite um **Email** válido.

    d. Selecione **Provedor de identidade SSO** como **SAML 2.0**.

4. Na página **Configuração do SSO**, realize as seguintes etapas:

    ![SSOConfig do Perception United States (não UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Selecione **Tipo de SAML NameID** como **EMAIL**.

    b. Na caixa de texto **Nome da configuração de SSO**, digite o nome da sua **Configuração**.
    
    c. Na caixa de texto **Nome do Provedor de Identidade**, cole o valor de **Identificador do Azure AD** copiado do portal do Azure. 

    d. Na **caixa de texto Domínio de SAML**, digite o domínio como @contoso.com.

    e. Clique em **Carregar novamente** para carregar o arquivo **XML de metadados**.

    f. Clique em **Atualizar**.

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

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure, concedendo acesso ao Perception United States (não UltiPro).

1. No portal do Azure, escolha **Aplicativos empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **Perception United States (não UltiPro)** .

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Perception United States (não UltiPro)** .

    ![O link do Perception United States (não UltiPro) na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Criar um usuário de teste do Perception United States (não UltiPro)

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Perception United States (não UltiPro). Trabalhe com a [equipe de suporte do Perception United States (não UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) para adicionar os usuários na plataforma do Perception United States (não UltiPro).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Perception United States (não UltiPro) no Painel de Acesso, você deverá ser conectado automaticamente ao Perception United States (não UltiPro) no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


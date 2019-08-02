---
title: 'Tutorial: integração do Azure Active Directory ao SciQuest Spend Director | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o SciQuest Spend Director.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 199f531485a6a6c9ea234b35a7dbb88b66b3067c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091649"
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Tutorial: integração do Active Directory do Azure ao SciQuest Spend Director

Neste tutorial, você aprenderá a integrar o SciQuest Spend Director ao Microsoft Azure AD (Azure Active Directory).
A integração do SciQuest Spend Director ao Azure AD oferece os seguintes benefícios:

* Você pode controlar, no Microsoft Azure AD, quem tem acesso ao SciQuest Spend Director.
* Você pode permitir que os usuários entrem automaticamente no SciQuest Spend Director (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SciQuest Spend Director, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do SciQuest Spend Director com o logon único habilitado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* SciQuest Spend Director dá suporte para SSO iniciado por **SP**
* SciQuest Spend Director dá suporte para provisionamento de usuário **Just-In-Time**

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Adição do SciQuest Spend Director por meio da galeria

Para configurar a integração do SciQuest Spend Director com o Azure AD, você precisa adicionar o SciQuest Spend Director, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o SciQuest Spend Director por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SciQuest Spend Director**, selecione **SciQuest Spend Director** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SciQuest Spend Director na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com SciQuest Spend Director, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SciQuest Spend Director.

Para configurar e testar o logon único do Azure AD com o SciQuest Spend Director, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do SciQuest Spend Director](#configure-sciquest-spend-director-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do SciQuest Spend Director](#create-sciquest-spend-director-test-user)** - para ter um equivalente de Brenda Fernandes no SciQuest Spend Director vinculado a sua representação Microsoft Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SciQuest Spend Director, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SciQuest Spend Director**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do SciQuest Spend Director](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.sciquest.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do SciQuest Spend Director](https://www.jaggaer.com/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar SciQuest Spend Director**, copie as URLs apropriadas, conforme sua necessidade.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-sciquest-spend-director-single-sign-on"></a>Configurar Logon Único do SciQuest Spend Director

Para configurar o logon único no lado do **SciQuest Spend Director**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do SciQuest Spend Director](https://www.jaggaer.com/contact-us/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao SciQuest Spend Director.

1. No portal do Azure, escolha **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, selecione **SciQuest Spend Director**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SciQuest Spend Director**.

    ![O link do SciQuest Spend Director na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sciquest-spend-director-test-user"></a>Criar usuário de teste do SciQuest Spend Director

O objetivo desta seção é criar um usuário chamado Britta Simon no SciQuest Spend Director.

Você precisa entrar em contato com a [equipe de suporte do SciQuest Spend Director](https://www.jaggaer.com/contact-us/) e fornecer a eles os detalhes da sua conta de teste para que ela seja criada.

Como alternativa, você também pode usar o provisionamento just-in-time, um recurso de logon único com suporte do SciQuest Spend Director.  
Se o provisionamento Just-In-Time estiver habilitado, os usuários serão automaticamente criados pelo SciQuest Spend Director durante uma tentativa de logon único, caso não existam. Este recurso elimina a necessidade de criar manualmente os usuários correspondentes ao logon único.

Para habilitar o provisionamento just-in-time, você precisa entrar em contato com a sua [equipe de suporte do SciQuest Spend Director](https://www.jaggaer.com/contact-us/).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SciQuest Spend Director no Painel de Acesso, você deverá ser conectado automaticamente ao SciQuest Spend Director para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
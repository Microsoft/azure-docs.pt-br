---
title: 'Tutorial: Integração do Azure Active Directory ao Signagelive | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c31f201179f9f0eb7077ed275aae927a2a4fe376
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090732"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Tutorial: Integração do Azure Active Directory ao Signagelive

Neste tutorial, você aprenderá a integrar o Signagelive ao Azure AD (Azure Active Directory).
A integração do Signagelive ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Signagelive.
* Você pode permitir que os usuários sejam conectados automaticamente ao Signagelive (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Signagelive, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, obtenha uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura habilitada para logon único do Signagelive.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Signagelive dá suporte ao SSO iniciado por SP.

## <a name="add-signagelive-from-the-gallery"></a>Adicionar o Signagelive por meio da galeria

Para configurar a integração do Signagelive ao Azure AD, primeiro adicione o Signagelive por meio da galeria à lista de aplicativos SaaS gerenciados.

Para adicionar o Signagelive por meio da galeria, execute as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Signagelive**. 

     ![Signagelive na lista de resultados](common/search-new-app.png)

5. Selecione **Signagelive** no painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Signagelive, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Signagelive.

Para configurar e testar o logon único do Azure AD com o Signagelive, você precisará concluir os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Configurar o logon único do Signagelive](#configure-signagelive-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
3. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Criar um usuário de teste do Signagelive](#create-a-signagelive-test-user) para ter um equivalente de Brenda Fernandes no Signagelive que esteja vinculado à representação de usuário do Azure AD.
6. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Signagelive, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Signagelive**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione **SAML** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com o SAML**, selecione **Editar** para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Signagelive](common/sp-signonurl.png)

    Na caixa **URL de Logon**, insira uma URL que use o seguinte padrão: `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Para obter o valor, contate a [equipe de suporte ao cliente do Signagelive](mailto:support@signagelive.com). Veja também os padrões exibidos na seção **Configuração Básica do SAML** no portal do Azure.

5. Na página **Configurar Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para baixar o **Certificado (Bruto)** usando as opções fornecidas de acordo com suas necessidades. Em seguida, salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

6. Na seção **Configurar o Signagelive**, copie as URLs necessárias.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-signagelive-single-sign-on"></a>Configurar o logon único do Signagelive

Para configurar o logon único no lado do Signagelive, envie o **Certificado (Bruto)** baixado e as URLs copiadas do portal do Azure para a [equipe de suporte do Signagelive](mailto:support@signagelive.com). Eles garantem que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, insira "brittasimon@yourcompanydomain.extension". Por exemplo, nesse caso, você poderá inserir "BrittaSimon@contoso.com".

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo o acesso ao Signagelive.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Signagelive**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Signagelive**.

    ![O link do Signagelive na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Selecione o botão **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, na lista **Usuários**, selecione **Brenda Fernandes**. Em seguida, clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.

### <a name="create-a-signagelive-test-user"></a>Criar um usuário de teste do Signagelive

Nesta seção, você criará um usuário chamado Brenda Fernandes no Signagelive. Trabalhe com a [equipe de suporte do Signagelive](mailto:support@signagelive.com) para adicionar os usuários à plataforma Signagelive. Você precisa criar e ativar os usuários antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Ao selecionar o bloco do **Signagelive** no portal Meus Aplicativos, você deverá ser conectado automaticamente. Para obter mais informações sobre o portal Meus Aplicativos, confira [O que é o portal Meus Aplicativos?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


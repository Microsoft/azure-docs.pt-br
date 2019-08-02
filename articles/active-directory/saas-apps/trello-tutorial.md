---
title: 'Tutorial: Integração do Azure Active Directory ao Trello | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 730ff5ff12f18d1f85b3ca53adb42fee41e19fb4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088287"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Tutorial: Integração do Azure Active Directory ao Trello

Neste tutorial, você aprenderá a integrar o Trello ao Azure AD (Azure Active Directory).
A integração do Trello ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Trello.
* Você pode permitir que os usuários sejam conectados automaticamente ao Trello (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Trello, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, obtenha uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura habilitada para logon único do Trello.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Trello dá suporte ao SSO iniciado por SP e IdP

* O Trello dá suporte ao provisionamento de usuário just-in-time

## <a name="add-trello-from-the-gallery"></a>Adicionar o Trello da galeria

Para configurar a integração do Trello ao Azure AD, primeiro adicione o Trello por meio da galeria à lista de aplicativos SaaS gerenciados.

Para adicionar o Trello por meio da galeria, execute as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Selecione **Aplicativos Empresariais** e, em seguida, selecione **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Trello** e, em seguida, selecione **Trello** no painel de resultados.

5. Selecione o botão **Adicionar** para adicionar o aplicativo.

     ![Trello na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Trello, com base em um usuário de teste chamado **Brenda Fernandes**.

Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Trello.

Para configurar e testar o logon único do Azure AD com o Trello, você precisa concluir os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Configurar o logon único do Trello](#configure-trello-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
3. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Criar um usuário de teste do Trello](#create-a-trello-test-user) para ter um equivalente de Brenda Fernandes no Trello que esteja vinculado à representação de usuário no Azure AD.
6. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

> [!NOTE]
> Você deve obter o campo de dados dinâmico **\<empresa\>** do Trello. Se você não tiver o valor do campo de dados dinâmico, contate a [equipe de suporte do Trello](mailto:support@trello.com) para obtê-lo para a sua empresa.

Para configurar o logon único do Azure AD com o Trello, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Trello**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de Logon único**, selecione **SAML** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar Logon Único com o SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por IdP execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Trello](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, insira uma URL usando o seguinte padrão: `https://trello.com/auth/saml/metadata`

    b. Na caixa **URL de Resposta**, insira uma URL usando o seguinte padrão: `https://trello.com/auth/saml/consume/<enterprise>`

5. Selecione **Definir URLs adicionais** e, em seguida, execute as seguintes etapas caso deseje configurar o aplicativo no modo iniciado por SP:

    ![Informações de logon único de Domínio e URLs do Trello](common/metadata-upload-additional-signon.png)

    Na caixa **URL de Logon**, insira uma URL usando o seguinte padrão: `https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do Trello](mailto:support@trello.com) para obter esses valores. Veja também os padrões na seção **Configuração Básica do SAML** no portal do Azure.

6. O aplicativo Trello espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração do aplicativo. Na página **Configurar Logon Único com o SAML**, selecione o botão **Editar** para abrir a caixa de diálogo **Atributos de Usuário**.

    ![A caixa de diálogo Atributos do Usuário](common/edit-attribute.png)

7. Na seção **Declarações do Usuário**, na caixa de diálogo **Atributos do Usuário**, configure o atributo do token SAML como mostrado na imagem anterior. Em seguida, execute as etapas a seguir:

    | NOME |  Atributo de Origem|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![Caixa de diálogo Declarações de usuário](common/new-save-attribute.png)

    ![Gerenciar declarações de usuário](common/new-attribute-details.png)

    b. Na caixa **Nome**, insira o nome do atributo mostrado para essa linha.

    c. Deixe **Namespace** em branco.

    d. Para **Origem**, selecione **Atributo**.

    e. Na lista **Atributo de origem**, insira o valor do atributo mostrado para essa linha.

    f. Selecione **Ok**.

    g. Clique em **Salvar**.

8. Na página **Configurar Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para baixar o **Certificado (Base64)** usando as opções fornecidas de acordo com suas necessidades. Em seguida, salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

9. Na seção **Configurar o Trello**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-trello-single-sign-on"></a>Configurar o logon único do Trello

Para configurar o logon único no lado do Trello, primeiro envie o **Certificado (Base64)** baixado e as URLs copiadas do portal do Azure para a [equipe de suporte do Trello](mailto:support@trello.com). Eles garantem que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

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

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Trello.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, **Trello**.

    ![Folha de Aplicativos Empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Trello**.

    ![O link do Trello na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Selecione o botão **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários. Em seguida, clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.

### <a name="create-a-trello-test-user"></a>Criar um usuário de teste do Trello

Nesta seção, você cria um usuário chamado Brenda Fernandes no Trello. O Trello dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Trello, um novo será criado após a autenticação.

> [!NOTE]
> Caso precise criar um usuário manualmente, contate a [equipe de suporte do Trello](mailto:support@trello.com).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Ao selecionar o bloco do Trello no portal Meus Aplicativos, você deverá ser conectado automaticamente ao Trello. Para obter mais informações sobre o portal Meus Aplicativos, confira [O que é o portal Meus Aplicativos?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


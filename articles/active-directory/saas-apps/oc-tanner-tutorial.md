---
title: 'Tutorial: Integração do Azure Active Directory com o O.C. Tanner - AppreciateHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o O.C. Tanner - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8814dc9ba94ca7fa56a2225c71895520467bf05f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095833"
---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Tutorial: Integração do Azure Active Directory com o O.C. Tanner - AppreciateHub

Neste tutorial, você aprenderá a integrar o O.C. Tanner - AppreciateHub ao Azure Active Directory (Azure AD)
Integração do O.C. O Tanner - AppreciateHub ao AD do Azure oferece os seguintes benefícios:

* Você pode controlar no AD do Azure quem terá acesso ao O.C. Tanner - AppreciateHub.
* É possível permitir que os usuários se conectem automaticamente ao O.C. Tanner – AppreciateHub (Logon Único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao O.C. Tanner - AppreciateHub, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* O.C. Assinatura habilitada para logon único do Tanner – AppreciateHub

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O O.C. Tanner – AppreciateHub dá suporte a SSO iniciado por **IDP**

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Adicionar o O.C. Tanner - AppreciateHub a partir da galeria

Para configurar a integração do O.C. Tanner - AppreciateHub no AD do Azure, você precisa adicionar O.C. Tanner - AppreciateHub a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o O.C. Tanner - AppreciateHub da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **O.C. Tanner – AppreciateHub**, selecione **O.C. Tanner – AppreciateHub** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![O O.C. Tanner – AppreciateHub na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, configura e testa o logon único do Azure AD com o O.C. Tanner – AppreciateHub com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do O.C. Tanner - o AppreciateHub precisa ser estabelecido.

Para configurar e testar o logon único do AD do Azure com O.C. Tanner - AppreciateHub, é necessário concluir os seguintes blocos de criação:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do O.C. Tanner – AppreciateHub](#configure-oc-tanner---appreciatehub-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do O.C. Tanner - AppreciateHub](#create-oc-tanner---appreciatehub-test-user)** - ter um equivalente de Brenda Fernandes no O.C. Tanner – AppreciateHub que é vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com O.C. Tanner – AppreciateHub, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **O.C. Tanner – AppreciateHub**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    >[!NOTE]
    >Baixe o **arquivo de metadados do provedor de serviços** [aqui](https://fed.appreciatehub.com/fed/sp/metadata)

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção "Configuração Básica do SAML".

     ![Informações de logon único de Domínio e URLs do O.C. Tanner – AppreciateHub](common/idp-intiated.png)

    > [!Note]
    > Se os valores de **Identificador** e **URL de Resposta** não forem preenchidos automaticamente, preencha-os manualmente de acordo com seus requisitos. Contate a [Equipe de suporte do O.C. Tanner – AppreciateHub Client](mailto:sso@octanner.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o O.C. Tanner – AppreciateHub**, copie as URLs corretas de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-oc-tanner---appreciatehub-single-sign-on"></a>Configurar o logon único do O.C. Tanner – AppreciateHub

Para configurar o logon único no lado do aplicativo do **O.C. Tanner – AppreciateHub**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs corretas copiadas do portal do Azure para a [equipe de suporte do O.C. Tanner – AppreciateHub](mailto:sso@octanner.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite um usuário, como BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você concederá a Brenda Fernandes acesso ao O.C. para que ela fique habilitada a usar o logon único do Azure. Tanner - AppreciateHub.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **O.C. Tanner - AppreciateHub**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **O.C. Tanner - AppreciateHub**.

    ![O link do O.C. Tanner – AppreciateHub na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>Criar um Usuário de teste do Tanner - AppreciateHub

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no O.C. Tanner - AppreciateHub.

**Para criar um usuário chamado Brenda Fernandes no O.C. Tanner - AppreciateHub, execute as seguintes etapas:**

Peça à sua [equipe de suporte do O.C. Tanner – AppreciateHub](mailto:sso@octanner.com) para criar um usuário que tenha o atributo nameID com o mesmo valor que o nome de usuário Brenda Fernandes no Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco O.C. Tanner – AppreciateHub no Painel de Acesso, você deverá ser conectado automaticamente ao O.C. Tanner – AppreciateHub no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Tutorial: Integração do Azure Active Directory ao OneTrust Privacy Management Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o software de gerenciamento de privacidade OneTrust.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 5fbec8b6a1a21826896f8e2499b1e8b7237d0ff9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095890"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Tutorial: Integração do Azure Active Directory ao OneTrust Privacy Management Software

Neste tutorial, você aprenderá como integrar o software de gerenciamento de privacidade OneTrust com o Azure Active Directory (Azure AD).
A integração do software de gerenciamento de privacidade OneTrust com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao software de gerenciamento de privacidade OneTrust.
* Você pode permitir que os usuários sejam conectados automaticamente ao Software de Gerenciamento de Privacidade OneTrust (Logon Único) com as respectivas contas do Azure Active Directory.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o software de gerenciamento de privacidade OneTrust, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do software de gerenciamento de privacidade OneTrust

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O software de gerenciamento de privacidade OneTrust é compatível com SSO iniciado por **SP** e **IDP**

* O software de gerenciamento de privacidade OneTrust é compatível com o provisionamento de usuário **Just In Time**

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Adicionando o software de gerenciamento de privacidade OneTrust por meio da Galeria

Para configurar a integração do software de gerenciamento de privacidade OneTrust no Azure AD, você precisa adicionar o software de gerenciamento de privacidade OneTrust da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o software de gerenciamento de privacidade OneTrust da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Software de gerenciamento de privacidade OneTrust**, selecione **Software de gerenciamento de privacidade OneTrust** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Software de gerenciamento de privacidade OneTrust na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure Active Directory com o software de gerenciamento de privacidade OneTrust, com base em um usuário de teste chamado **Brenda Fernandes**.
Para um logon único funcionar, uma relação de link entre um usuário do Azure Active Directory e o usuário relacionado no software de gerenciamento de privacidade OneTrust precisa ser estabelecida.

Para configurar e testar o logon único do Azure AD com o software de gerenciamento de privacidade OneTrust, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do software de gerenciamento de privacidade OneTrust](#configure-onetrust-privacy-management-software-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do software de gerenciamento de privacidade OneTrust](#create-onetrust-privacy-management-software-test-user)**  – ter um equivalente de Brenda Fernandes no software de gerenciamento de privacidade OneTrust que esteja vinculado à representação do usuário no Azure Active Directory.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure Active Directory com o software de gerenciamento de privacidade OneTrust, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Software de gerenciamento de privacidade OneTrust**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de domínio e URLs do software de gerenciamento de privacidade OneTrust](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `https://www.onetrust.com/saml2`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de domínio e URLs do software de gerenciamento de privacidade OneTrust](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do software de gerenciamento de privacidade OneTrust](mailto:support@onetrust.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o software de gerenciamento de privacidade OneTrust**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-onetrust-privacy-management-software-single-sign-on"></a>Configurar o logon único do software de gerenciamento de privacidade OneTrust

Para configurar o logon único no lado do **software de gerenciamento de privacidade OneTrust**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do software de gerenciamento de privacidade OneTrust](mailto:support@onetrust.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

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

Nesta seção, você habilitará a Brenda Fernandes para usar logon único do Azure, concedendo acesso ao software de gerenciamento de privacidade OneTrust.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **software de gerenciamento de privacidade OneTrust**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Software de gerenciamento de privacidade OneTrust**.

    ![O link do software de gerenciamento de privacidade OneTrust na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Criar usuário de teste do software de gerenciamento de privacidade OneTrust

Nesta seção, um usuário chamado Brenda Fernandes é criado no software de gerenciamento de privacidade OneTrust. O software de gerenciamento de privacidade OneTrust é compatível com o provisionamento Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no software de privacidade OneTrust, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do software de gerenciamento de privacidade OneTrust](mailto:support@onetrust.com).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do software de gerenciamento de privacidade OneTrust no painel de acesso, deverá ser conectado automaticamente ao software de gerenciamento de privacidade OneTrust para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


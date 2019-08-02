---
title: 'Tutorial: Integração do Azure Active Directory ao Replicon | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092760"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Tutorial: Integrar o Replicon ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Replicon ao Azure AD (Azure Active Directory). Com a integração do Replicon ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Replicon.
* Permitir que os usuários entrem automaticamente no Replicon com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura habilitada para logon único (SSO) do Replicon.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Replicon dá suporte a SSO iniciado por **SP**.

## <a name="adding-replicon-from-the-gallery"></a>Adicionando Replicon a partir da galeria

Para configurar a integração do Replicon ao Microsoft Azure AD, você precisará adicionar o Replicon a partir da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **Replicon** na caixa de pesquisa.
1. Selecione **Replicon** no painel de resultados e, depois, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Replicon usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Replicon.

Para configurar e testar o SSO do Azure AD com o Replicon, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Replicon](#configure-replicon-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Replicon](#create-replicon-test-user)** – para ter um equivalente de B.Fernandes no Replicon que esteja vinculado à representação de usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Replicon**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    1. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://global.replicon.com/!/saml2/<client name>`

    1. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Replicon](https://www.replicon.com/customerzone/contact-support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Clique no ícone de edição/caneta do **Certificado de Autenticação SAML** para editar as configurações.

    ![Algoritmo de assinatura](common/signing-algorithm.png)

    1. Selecione **Assinar declaração SAML** como a **Opção de Assinatura**.

    1. Selecione **SHA-256** como o **Algoritmo de Assinatura**.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, localize **XML de Metadados de Federação** e escolha **Download** para fazer o download do certificado e salve-o em seu computador.

   ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Configurar o SSO do Replicon

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa no Replicon como administrador.

2. Para configurar o SAML 2.0, execute as seguintes etapas:

    ![Habilitar autenticação de SAML](./media/replicon-tutorial/ic777805.png "Habilitar autenticação de SAML")

    a. Para exibir a caixa de diálogo **EnableSAML Authentication2**, acrescente o seguinte à URL, após a chave da empresa: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * A seguir, é mostrado o esquema do URL completo: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Clique em **+** para expandir a seção **v20Configuration**.

   c. Clique em **+** para expandir a seção **metaDataConfiguration**.

   d. Selecione **SHA256** para xmlSignatureAlgorithm

   e. Clique em **Escolher Arquivo** para selecionar o arquivo XML de metadados de provedor de identidade e clique em **Enviar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Replicon.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Replicon**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-replicon-test-user"></a>Criar um usuário de teste do Replicon

O objetivo desta seção é criar um usuário chamado B.Fernandes no Replicon.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Em uma janela de navegador da Web, faça logon no site de sua empresa do Replicon como administrador.

2. Vá para **Administração\>Usuários**.

    ![Usuários](./media/replicon-tutorial/ic777806.png "Usuários")

3. Clique em **+Adicionar Usuário**.

    ![Adicionar Usuário](./media/replicon-tutorial/ic777807.png "Adicionar Usuário")

4. Na seção **Perfil de Usuário** , realize as seguintes etapas:

    ![Perfil de usuário](./media/replicon-tutorial/ic777808.png "Perfil de usuário")

    a. Na caixa de texto **Nome de Logon**, digite o endereço de email do Microsoft Azure Active Directory para o usuário do que você deseja provisionar, como `B.Simon@contoso.com`.

    > [!NOTE]
    > O Nome de Logon precisa corresponder ao endereço de email do usuário no AD do Azure

    b. Para **Tipo de Autenticação**, selecione **SSO**.

    c. Defina a ID de Autenticação com o mesmo valor como o Nome de Logon (o endereço de email do Azure AD do usuário)

    d. Na caixa de texto **Departamento** , digite o departamento do usuário.

    e. Para **Tipo de Funcionário**, selecione **Administrador**.

    f. Clique em **Salvar Perfil do Usuário**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Replicon ou as APIs fornecidas pelo Replicon para provisionar as contas de usuário do Microsoft Azure AD.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco Replicon no Painel de Acesso, você deverá entrar automaticamente no Replicon, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
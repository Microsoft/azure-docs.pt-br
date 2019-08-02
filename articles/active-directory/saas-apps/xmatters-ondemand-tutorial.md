---
title: 'Tutorial: Integração do Azure Active Directory com o xMatters OnDemand | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e8ae31122d59238ac104d7d873cf56f32977c9af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086509"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Integração do Azure Active Directory com o xMatters OnDemand

Neste tutorial, você aprenderá como integrar o xMatters OnDemand ao Azure Active Directory (Azure AD).
A integração do xMatters OnDemand ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao xMatters OnDemand.
* Você pode permitir que os usuários façam logon automaticamente no xMatters OnDemand (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o xMatters OnDemand, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do xMatters OnDemand habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O xMatters OnDemand dá suporte ao SSO iniciado por **IDP**

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Adicionar o xMatters OnDemand da galeria

Para configurar a integração do xMatters OnDemand com o Azure AD, é necessário adicionar o xMatters OnDemand da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o xMatters OnDemand da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **xMatters OnDemand**, selecione **xMatters OnDemand** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![xMatters OnDemand na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o xMatters OnDemand com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no xMatters OnDemand.

Para configurar e testar o logon único do Azure AD com o xMatters OnDemand, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do xMatters OnDemand](#configure-xmatters-ondemand-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do xMatters OnDemand](#create-xmatters-ondemand-test-user)** – para ter um equivalente de Brenda Fernandes no xMatters OnDemand vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o xMatters OnDemand, siga as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **xMatters OnDemand**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do xMatters OnDemand](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|
    | |

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|
    | |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do xMatters OnDemand](https://www.xmatters.com/company/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

    > [!IMPORTANT]
    > É necessário encaminhar o certificado para a [equipe de suporte do xMatters OnDemand](https://www.xmatters.com/company/contact-us/). O certificado precisa ser carregado pela equipe de suporte do xMatters antes de você finalizar a configuração de logon único.

6. Na seção **Configurar o xMatters OnDemand**, copie a URL apropriada de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Configurar o logon único do xMatters OnDemand

1. Em uma janela diferente do navegador da Web, entre no seu site de empresa no xMatters OnDemand como administrador.

2. Na barra de ferramentas na parte superior, clique em **Administrador** e em **Detalhes da Empres**a na barra de navegação à esquerda.

    ![Admin](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

3. Na página **Configuração do SAML** , realize as seguintes etapas:

    ![Configuração SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Configuração SAML")

    a. Selecione **Habilitar SAML**.

    b. Na caixa de texto **ID do Provedor de Identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logon Único**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    d. Na caixa de texto **URL de Logoff Único**, cole o valor da **URL de Logoff** que você copiou do portal do Azure.

    e. Na página Detalhes da Empresa, na parte superior, clique em **Salvar Alterações**.

    ![Detalhes da empresa](./media/xmatters-ondemand-tutorial/IC776797.png "Detalhes da empresa")

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao xMatters OnDemand.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **xMatters OnDemand**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **xMatters OnDemand**.

    ![O link do xMatters OnDemand na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-xmatters-ondemand-test-user"></a>Criar usuário de teste do xMatters OnDemand

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no xMatters OnDemand.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Entre em seu locatário do **xMatters OnDemand**.

2. Clique na guia **Usuários** e, em seguida, **Adicionar Usuário**.

    ![Usuários](./media/xmatters-ondemand-tutorial/IC781048.png "Usuários")

3. Na seção **Adicionar um Usuário** , realize as seguintes etapas:

    ![Adicionar um Usuário](./media/xmatters-ondemand-tutorial/IC781049.png "Adicionar um Usuário")

    a. Selecione **Ativo**.

    b. Na caixa de texto **ID de usuário**, digite a ID de usuário, como Brittasimon@contoso.com.

    c. Na caixa de texto **Nome**, digite o nome do usuário, como Brenda.

    d. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como Fernandes.

    e. Na caixa de texto **Site**, insira o site válido de uma conta válida do Azure AD que você deseja provisionar.

    f. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco do xMatters OnDemand no Painel de Acesso, você deverá ser automaticamente conectado ao xMatters OnDemand para o qual você tiver configurado o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


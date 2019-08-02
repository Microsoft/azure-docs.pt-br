---
title: 'Tutorial: Integração do Azure Active Directory com o ZPA (Zscaler Private Access) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ZPA (Zscaler Private Access).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67085708"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Tutorial: Integração do ZPA (Zscaler Private Access) ao Azure AD (Azure Active Directory)

Neste tutorial, você aprenderá a integrar o ZPA (Zscaler Private Access) ao Azure AD (Azure Active Directory). Quando você integra o ZPA (Zscaler Private Access) ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao ZPA (Zscaler Private Access).
* Permitir que os usuários sejam conectados automaticamente ao ZPA (Zscaler Private Access) com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do ZPA (Zscaler Private Access).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O ZPA (Zscaler Private Access) dá suporte ao SSO iniciado por **SP**.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Adicionando ZPA (Zscaler Private Access) da galeria

Para configurar a integração do ZPA (Zscaler Private Access) ao Azure AD, você precisará adicionar o ZPA (Zscaler Private Access) da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ZPA (Zscaler Private Access)** na caixa de pesquisa.
1. Escolha **ZPA (Zscaler Private Access)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o ZPA (Zscaler Private Access) usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ZPA (Zscaler Private Access).

Para configurar e testar o logon único do Azure AD com o ZPA (Zscaler Private Access), conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar o ZPA (Zscaler Private Access)](#configure-zscaler-private-access-zpa)** para definir as configurações de logon único no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do ZPA (Zscaler Private Access)](#create-zscaler-private-access-zpa-test-user)** para ter um equivalente de Brenda Fernandes no ZPA (Zscaler Private Access) que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ZPA (Zscaler Private Access)** , localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > O valor da **URL de logon** não é real. Atualize o valor com a URL de Entrada real. Contate a [equipe de suporte ao cliente do ZPA (Zscaler Private Access)](https://help.zscaler.com/zpa-submit-ticket) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o ZPA (Zscaler Private Access)** , copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Configurar o ZPA (Zscaler Private Access)

1. Para automatizar a configuração no ZPA (Zscaler Private Access), é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Configurar o ZPA (Zscaler Private Access)** direcionará você ao aplicativo ZPA (Zscaler Private Access). Lá, forneça as credenciais de administrador para entrar no ZPA (Zscaler Private Access). A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o ZPA (Zscaler Private Access) manualmente, abra uma nova janela do navegador da Web, entre no site empresarial do ZPA (Zscaler Private Access) como administrador e execute as seguintes etapas:

4. No menu à esquerda, clique em **Administração**, navegue até a seção **AUTENTICAÇÃO** e clique em **Configuração de IdP**.

    ![Administração do Administrador do Zscaler Private Access](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. No canto superior direito, clique em **Adicionar Configuração de IdP**. 

    ![idp do Administrador do Zscaler Private Access](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Na página **Adicionar Configuração de IdP**, execute as seguintes etapas:
 
    ![seleção do Administrador do Zscaler Private Access](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Clique em **Selecionar Arquivo** para carregar o arquivo de metadados baixado do Azure AD no campo **Upload de Arquivo de Metadados de IdP**.

    b. Ele lê os **metadados de IdP** do Azure AD e popula todas as informações dos campos, conforme mostrado abaixo.

    ![configuração do Administrador do Zscaler Private Access](./media/zscalerprivateaccess-tutorial/config.png)

    c. Selecione seu domínio do campo **Domínios**.
    
    d. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao ZPA (Zscaler Private Access).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ZPA (Zscaler Private Access)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Criar um usuário de teste do ZPA (Zscaler Private Access)

Nesta seção, você deve criar uma usuária chamada Brenda Fernandes no ZPA (Zscaler Private Access). Trabalhe com [equipe de suporte do ZPA (Zscaler Private Access)](https://help.zscaler.com/zpa-submit-ticket) para adicionar os usuários na plataforma ZPA (Zscaler Private Access).

### <a name="test-sso"></a>Testar o SSO

Ao escolher o bloco do ZPA (Zscaler Private Access) no Painel de Acesso, você deverá ser conectado automaticamente ao ZPA (Zscaler Private Access) para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
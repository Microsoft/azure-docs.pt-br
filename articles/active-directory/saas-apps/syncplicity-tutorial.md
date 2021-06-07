---
title: 'Tutorial: Integração do Azure Active Directory com o Syncplicity | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Syncplicity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 3c665795325ed3863583eb0f21f3e0d3f534154a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201449"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Tutorial: Integrar o Syncplicity ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Syncplicity ao Azure AD (Azure Active Directory). Com a integração do Syncplicity ao Azure AD, você poderá:

* Controle com o Azure AD quem tem acesso ao Syncplicity.
* Permitir que os usuários se conectem automaticamente ao Syncplicity com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso não tenha uma assinatura, obtenha uma avaliação gratuita de 12 meses [aqui](https://azure.microsoft.com/free/).
* Uma assinatura do Syncplicity habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Syncplicity é compatível com o SSP iniciado por **SP**.

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionar o Syncplicity da galeria

Para configurar a integração do Syncplicity ao Azure AD, você precisará adicionar o Syncplicity da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Em **Criar**, clique em **Aplicativo Empresarial**.
1. Na seção **Procurar na Galeria do Azure AD**, digite **Syncplicity** na caixa de pesquisa.
1. Selecione **Syncplicity** no painel de resultados e clique em **Criar** para adicionar o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o Syncplicity usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Syncplicity.

Para configurar e testar o SSO do Azure AD com o Syncplicity, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Syncplicity](#configure-syncplicity-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Syncplicity](#create-syncplicity-test-user)** – para ter um equivalente de B.Fernandes no Syncplicity que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.
7. **[Atualizar SSO](#update-sso)** – para fazer as alterações necessárias no Syncplicity se você tiver alterado as configurações de SSO no Azure AD.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Syncplicity**, localize a seção **Introdução** e selecione **Configurar logon único**.
2. Na página **Escolher um método de logon único**, escolha **SAML**.
3. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.syncplicity.com/sp`

    b. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.syncplicity.com`
    
    c. Na caixa de texto **URL de Resposta (URL do Serviço do Consumidor de Declaração)** digite uma URL usando o seguinte padrão: `https://<companyname>.syncplicity.com/Auth/AssertionConsumerService.aspx`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do cliente Syncplicity](https://www.syncplicity.com/contact-us) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Editar**. Em seguida, na caixa de diálogo, clique no botão de reticências ao lado do certificado ativo e selecione **Download de certificado PEM**.

   ![O link de download do Certificado](common/certificatebase64.png)

    > [!NOTE]
    > Você precisa do certificado PEM, pois o Syncplicity não aceita certificados no formato CER.

6. Na seção **Configurar Syncplicity**, copie as URLs apropriadas com base nas suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso&quot;></a>Configurar SSO do Syncplicity

1. Entre no seu locatário do **Syncplicity**.

1. No menu, na parte superior, clique em **Administrador**, selecione **Configurações** e clique em **Domínio personalizado e logon único**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png &quot;Syncplicity")

1. Na página da caixa de diálogo **Logon Único (SSO)**, execute as etapas a seguir:

    ![Logon Único \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Na caixa de texto **Domínio personalizado**, digite o nome do seu domínio.
  
    b. Selecione **Habilitado** como **Status do logon único**.

    c. Na caixa de texto **ID da entidade**, cole o valor do **Identificador (ID da entidade)** que você usou na **Configuração de SAML Básica** no portal do Azure.

    d. Na caixa de texto **URL da página de entrada**, cole a **URL de entrada** copiada do portal do Azure.

    e. Na caixa de texto **URL da página de logoff**, cole a **URL de logoff** copiada do portal do Azure.

    f. Em **Certificado do Provedor de Identidade**, clique em **Escolher Arquivo** e carregue o certificado que você baixou do portal do Azure.

    g. Clique em **SALVAR ALTERAÇÕES**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
2. Selecione **Novo usuário** na parte superior da tela.
3. Nas propriedades do **Usuário**, siga estas etapas:

   a. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.

   b. No campo **Nome**, insira `B.Simon`.  
   
   c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   
   d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder a ela o acesso ao Syncplicity.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Syncplicity**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário/grupo**.

    ![O link Adicionar Usuário](common/add-assign-user.png)
1. Na página **Adicionar Atribuição**, selecione **Usuários**. 
1. Na caixa de diálogo **Usuários**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na página **Adicionar Atribuição**, clique no botão **Atribuir**.

### <a name="create-syncplicity-test-user"></a>Criar usuário de teste do Syncplicity

Para que os usuários do Azure AD possam entrar, eles precisam ser provisionados no aplicativo Syncplicity. Esta seção descreve como criar contas de usuário do Azure AD no Syncplicity.

**Para provisionar uma conta de usuário no Syncplicity, execute as seguintes etapas:**

1. Entre no locatário do **Syncplicity** (por exemplo, `https://company.Syncplicity.com`).

1. Clique em **Administrador**, selecione **Contas de usuário** e clique em **Adicionar um usuário**.

    ![Gerenciar usuários](./media/syncplicity-tutorial/ic769764.png "Gerenciar Usuários")

1. Digite os **Endereços de email** de uma conta do Azure AD que você deseja provisionar, selecione **Usuário** como **Função** e clique em **Avançar**.

    ![Informações da conta](./media/syncplicity-tutorial/ic769765.png "Informações da conta")

    > [!NOTE]
    > O titular da conta do Azure AD recebe um email com um link para confirmar e ativar a conta.

1. Selecione um grupo na sua empresa ao qual quer que o novo usuário se torne membro e, em seguida, clique em **Avançar**.

    ![Associação de grupo](./media/syncplicity-tutorial/ic769772.png "Associação de grupo")

    > [!NOTE]
    > Se não houver nenhum grupo listado, basta clicar em **Avançar**.

1. Selecione as pastas que você gostaria de colocar sob controle do Syncplicity no computador do usuário e, em seguida, clique em **Avançar**.

    ![Pastas do Syncplicity](./media/syncplicity-tutorial/ic769773.png "Pastas do Syncplicity")

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Syncplicity ou as APIs fornecidas pelo Syncplicity para provisionar as contas de usuário do Azure AD.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Syncplicity no Painel de Acesso, você deverá entrar automaticamente no Syncplicity no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

### <a name="update-sso"></a>Atualizar SSO

Sempre que precisar fazer alterações no SSO, você precisará verificar o **Certificado de Autenticação SAML** que está sendo usado. Se o certificado tiver sido alterado, carregue o novo no Syncplicity, conforme descrito em **[Configurar o SSO do Syncplicity](#configure-syncplicity-sso)** .

Se estiver usando o aplicativo móvel do Syncplicity, entre em contato com o suporte ao cliente do Syncplicity (support@syncplicity.com) para obter assistência.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

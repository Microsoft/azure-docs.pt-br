---
title: 'Tutorial: Integração do Azure Active Directory com o Adaptive Insights | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adaptive Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 599b0c8f45f91f9ecff210264a813e302f18059e
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488898"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Tutorial: Integrar o Adaptive Insights ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Adaptive Insights ao Azure AD (Azure Active Directory). Ao integrar o Adaptive Insights ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Adaptive Insights.
* Permitir que os usuários sejam conectados automaticamente ao Adaptive Insights com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Adaptive Insights.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Adaptive Insights é compatível com SSO iniciado por **IDP**

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adicionando o Adaptive Insights da galeria

Para configurar a integração do Adaptive Insights com o Azure AD, você precisa adicionar o Adaptive Insights por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Adaptive Insights** na caixa de pesquisa.
1. Selecione **Adaptive Insights** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Adaptive Insights usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adaptive Insights.

Para configurar e testar o SSO do Azure AD com o Adaptive Insights, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Adaptive Insights](#configure-adaptive-insights-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Adaptive Insights](#create-adaptive-insights-test-user)** – para ter um equivalente de B.Fernandes no Adaptive Insights que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Adaptive Insights**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, execute as seguintes etapas:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > É possível obter valores de URL de resposta e Identificador (ID da entidade) na página **Configurações de SSO do SAML** do Adaptive Insights.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Adaptive Insights**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Configurar o SSO do Adaptive Insights

1. Em outra janela do navegador da Web, faça logon como administrador no site de sua empresa no Adaptive Insights.

2. Vá para **Administração**.

    ![Admin](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. Na seção **Usuários e Funções**, clique em **Configurações de SSO do SAML**.

    ![Gerenciar Configurações de SSO do SAML](./media/adaptivesuite-tutorial/ic805645.png "Gerenciar Configurações de SSO do SAML")

4. Na página **Configurações de SSO do SAML** , realize as seguintes etapas:

    ![Configurações de SSO do SAML](./media/adaptivesuite-tutorial/ic805646.png "Configurações de SSO do SAML")

    a. Na caixa de texto **Nome do provedor de identidade** , digite um nome para a sua configuração.

    b. Cole o valor do **Identificador do Azure AD** copiado do portal do Azure na caixa de texto **ID da Entidade do Provedor de identidade**.

    c. Cole o valor da **URL de Logon** copiado do Portal do Azure na caixa de texto **URL de SSO do Provedor de identidade**.

    d. Cole o valor da **URL de Logoff** copiado do Portal do Azure na caixa de texto **URL de Logoff personalizado**.

    e. Para carregar seu certificado baixado, clique em **Escolher arquivo**.

    f. Selecione as seguintes opções para:

     * **ID de usuário do SAML**, selecione **Nome de usuário do Adaptive Insights do Usuário**.

     * **Local da ID de usuário do SAML**, selecione **ID de usuário em NameID da Entidade**.

     * **Formato de NameID do SAML**, selecione **Endereço de email**.

     * **Habilitar SAML**, selecione **Permitir SSO do SAML e direcionar logon do Adaptive Insights**.

    g. Copie a **URL de SSO do Adaptive Insights** e cole-a nas caixas de texto **Identificador (ID da Entidade)** e **URL de Resposta** na seção **Configuração Básica do SAML** no portal do Azure.

    h. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Adaptive Insights.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Adaptive Insights**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-adaptive-insights-test-user"></a>Criar usuário de teste do Adaptive Insights

Para permitir que os usuários do Azure AD entrem no Adaptive Insights, eles devem ser provisionados no Adaptive Insights. No caso do Adaptive Insights, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Entre como administrador no site de sua empresa no **Adaptive Insights**.

2. Vá para **Administração**.

   ![Admin](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. Na seção **Usuários e Funções**, clique em **Usuário**.

   ![Adicionar Usuário](./media/adaptivesuite-tutorial/IC805648.png "Adicionar Usuário")

4. Na seção **Novo Usuário** , realize as seguintes etapas:

   ![Enviar](./media/adaptivesuite-tutorial/IC805649.png "Enviar")

   a. Digite o **Nome**, o **Nome de Usuário**, o **Email** e a **Senha** de um usuário válido do Azure Active Directory que deseja provisionar nas caixas de texto relacionadas.

   b. Selecione uma **Função**.

   c. Clique em **Enviar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação de conta de usuário do Adaptive Insights ou as APIs fornecidas pelo Adaptive Insights para provisionar as contas de usuário do AAD.

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Adaptive Insights no Painel de Acesso, você deverá ser conectado automaticamente ao Adaptive Insights no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Tutorial: Integração do Azure Active Directory com o Adaptive Insights | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adaptive Insights.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 6372cd9d778210163c461c55119343e6c6911e4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649008"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Tutorial: Integrar o Adaptive Insights ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Adaptive Insights ao Azure AD (Azure Active Directory). Ao integrar o Adaptive Insights ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Adaptive Insights.
* Permitir que os usuários sejam conectados automaticamente ao Adaptive Insights com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Adaptive Insights.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Adaptive Insights é compatível com SSO iniciado por **IDP**

## <a name="add-adaptive-insights-from-the-gallery"></a>Adicionar o Adaptive Insights por meio da galeria

Para configurar a integração do Adaptive Insights com o Azure AD, você precisa adicionar o Adaptive Insights por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Adaptive Insights** na caixa de pesquisa.
1. Selecione **Adaptive Insights** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Configurar e testar o SSO do Azure AD para o Adaptive Insights

Configure e teste o SSO do Azure AD com o Adaptive Insights usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adaptive Insights.

Para configurar e testar o SSO do Azure AD com o Adaptive Insights, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Adaptive Insights](#configure-adaptive-insights-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Adaptive Insights](#create-adaptive-insights-test-user)** – para ter um equivalente de B.Fernandes no Adaptive Insights que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Adaptive Insights**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > É possível obter valores de URL de resposta e Identificador (ID da entidade) na página **Configurações de SSO do SAML** do Adaptive Insights.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Adaptive Insights**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Adaptive Insights.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Adaptive Insights**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name=&quot;configure-adaptive-insights-sso&quot;></a>Configurar o SSO do Adaptive Insights

1. Em outra janela do navegador da Web, faça logon como administrador no site de sua empresa no Adaptive Insights.

2. Vá para **Administração**.

    ![Captura de tela que realça Administração no painel de navegação.](./media/adaptivesuite-tutorial/administration.png &quot;Admin")

3. Na seção **Usuários e Funções**, clique em **Configurações de SSO do SAML**.

    ![Gerenciar as Configurações de SSO do SAML](./media/adaptivesuite-tutorial/settings.png "Gerenciar Configurações de SSO do SAML")

4. Na página **Configurações de SSO do SAML** , realize as seguintes etapas:

    ![Configurações de SSO do SAML](./media/adaptivesuite-tutorial/saml.png "Configurações de SSO do SAML")

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

### <a name="create-adaptive-insights-test-user"></a>Criar usuário de teste do Adaptive Insights

Para permitir que os usuários do Azure AD entrem no Adaptive Insights, eles devem ser provisionados no Adaptive Insights. No caso do Adaptive Insights, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Entre como administrador no site de sua empresa no **Adaptive Insights**.

2. Vá para **Administração**.

   ![Administrador](./media/adaptivesuite-tutorial/administration.png "Admin")

3. Na seção **Usuários e Funções**, clique em **Usuário**.

   ![Adicionar Usuário](./media/adaptivesuite-tutorial/users.png "Adicionar usuário")

4. Na seção **Novo Usuário** , realize as seguintes etapas:

   ![Enviar](./media/adaptivesuite-tutorial/new.png "Enviar")

   a. Digite o **Nome**, o **Nome de Usuário**, o **Email** e a **Senha** de um usuário válido do Azure Active Directory que deseja provisionar nas caixas de texto relacionadas.

   b. Selecione uma **Função**.

   c. Clique em **Enviar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação de conta de usuário do Adaptive Insights ou as APIs fornecidas pelo Adaptive Insights para provisionar as contas de usuário do Azure AD.

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao Adaptive Insights para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Adaptive Insights em Meus Aplicativos, você deverá ser conectado automaticamente ao Adaptive Insights para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Adaptive Insights, você poderá impor controles de sessão, que protegem contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Count Me In – Operations Dashboard   Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Count Me In – Operations Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/30/2020
ms.author: jeedes
ms.openlocfilehash: 3339516193af6e1ff832ac586f4a81f8799c5b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727670"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-count-me-in---operations-dashboard"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Count Me In – Operations Dashboard

Neste tutorial, você aprenderá a integrar o Count Me In – Operations Dashboard ao Azure AD (Azure Active Directory). Ao integrar o Count Me In – Operations Dashboard ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Count Me In – Operations Dashboard.
* Permitir que os usuários sejam conectados automaticamente ao Count Me In – Operations Dashboard com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Count Me In – Operations Dashboard habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Count Me In – Operations Dashboard dá suporte ao SSO iniciado por **SP**

## <a name="adding-count-me-in---operations-dashboard-from-the-gallery"></a>Adicionar o Count Me In – Operations Dashboard por meio da galeria

Para configurar a integração do Count Me In – Operations Dashboard ao Azure AD, você precisará adicioná-lo por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Count Me In – Operations Dashboard** na caixa de pesquisa.
1. Selecione **Count Me In – Operations Dashboard** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-count-me-in---operations-dashboard"></a>Configurar e testar o SSO do Azure AD para o Count Me In – Operations Dashboard

Configure e teste o SSO do Azure AD com o Count Me In – Operations Dashboard usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Count Me In – Operations Dashboard.

Para configurar e testar o SSO do Azure AD com o Count Me In – Operations Dashboard, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Count Me In – Operations Dashboard](#configure-count-me-in-operations-dashboard-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Count Me In – Operations Dashboard](#create-count-me-in-operations-dashboard-test-user)** – para ter um equivalente de B.Fernandes no Count Me In – Operations Dashboard que esteja vinculado à declaração do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Count Me In – Operations Dashboard**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `api-us.localz.io/<PROJECT_ID>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do Count Me In – Operations Dashboard](mailto:support@localz.co) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Count Me In – Operations Dashboard espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Count Me In – Operations Dashboard espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ----------- | --------- |
    | funções atribuídas | user.assignedroles |

    > [!NOTE]
    > O Count Me In – Operations Dashboard espera funções para os usuários atribuídos ao aplicativo. Configure essas funções no Azure AD para que os usuários possam ser atribuídos às funções apropriadas. Para entender como configurar funções no Azure AD, confira [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Count Me In – Operations Dashboard**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)
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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso ao Count Me In – Operations Dashboard.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Count Me In – Operations Dashboard**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-count-me-in-operations-dashboard-sso"></a>Configurar o SSO do Count Me In – Operations Dashboard

Para configurar o logon único no lado do **Count Me In – Operations Dashboard**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Count Me In – Operations Dashboard](mailto:support@localz.co). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-count-me-in-operations-dashboard-test-user"></a>Criar um usuário de teste do Count Me In – Operations Dashboard

Nesta seção, você cria uma usuária chamada Brenda Fernandes no Count Me In – Operations Dashboard. Trabalhe com a [equipe de suporte do Count Me In – Operations Dashboard](mailto:support@localz.co) para adicionar os usuários na plataforma Count Me In – Operations Dashboard. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Count Me In – Operations Dashboard, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Count Me In – Operations Dashboard, na qual você iniciará o fluxo de logon.

* Você pode usar os Meus Aplicativos da Microsoft. Quando clicar no bloco do Count Me In – Operations Dashboard em Meus Aplicativos, você será redirecionado à URL de Logon no Count Me In – Operations Dashboard. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o Count Me In – Operations Dashboard, você poderá impor controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

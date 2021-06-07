---
title: 'Tutorial: Integração do Azure Active Directory com o LogicMonitor | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LogicMonitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d5342782c26b5c274699bacc4ea0c7cdf5b7f880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649395"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutorial: Integração do Active Directory do Azure com o LogicMonitor

Neste tutorial, você aprenderá a integrar o LogicMonitor ao Azure AD (Azure Active Directory). Ao integrar o LogicMonitor ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao LogicMonitor.
* Permitir que os usuários sejam conectados automaticamente ao LogicMonitor com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao LogicMonitor, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Caso não tenha um ambiente do Azure AD, obtenha uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura do LogicMonitor habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O LogicMonitor dá suporte ao SSO iniciado por **SP**

## <a name="add-logicmonitor-from-the-gallery"></a>Adicionar o LogicMonitor por meio da galeria

Para configurar a integração do LogicMonitor ao Azure AD, você precisará adicionar o LogicMonitor da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **LogicMonitor** na caixa de pesquisa.
1. Selecione **LogicMonitor** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>Configurar e testar o SSO do Azure AD para o LogicMonitor

Configure e teste o SSO do Azure AD com o LogicMonitor usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LogicMonitor.

Para configurar e testar o SSO do Azure AD com o LogicMonitor, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do LogicMonitor](#configure-logicmonitor-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do LogicMonitor](#create-logicmonitor-test-user)** : para ter um equivalente de B.Fernandes no LogicMonitor que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **LogicMonitor**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do LogicMonitor](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.logicmonitor.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.logicmonitor.com`
    
    c. Na caixa de texto **URL de Resposta (URL do Serviço do Consumidor de Declaração)** , digite uma URL usando o seguinte padrão: `https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do cliente do LogicMonitor](https://www.logicmonitor.com/contact/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o LogicMonitor**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao LogicMonitor.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **LogicMonitor**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name=&quot;configure-logicmonitor-sso&quot;></a>Configurar o SSO do LogicMonitor

1. Faça o logon no seu site da empresa **LogicMonitor** como um administrador.

2. No menu na parte superior, clique em **Configurações**.

    ![Configurações](./media/logicmonitor-tutorial/ic790052.png &quot;Configurações")

3. Na barra de navegação à esquerda, clique em **Logon Único**.

    ![Logon único](./media/logicmonitor-tutorial/ic790053.png "Logon único")

4. Na seção **Configurações de Logon Único (SSO)**, execute as etapas a seguir:

    ![Configurações de logon único](./media/logicmonitor-tutorial/ic790054.png "Configurações de logon único")

    a. Selecione **Habilitar Logon Único**.

    b. Para **Atribuição de papel padrão**, selecione **somente leitura**.

    c. Abra o arquivo de metadados no bloco de notas e cole o conteúdo do arquivo na caixa de texto **Metadados do provedor de identidade**.

    d. Clique em **Salvar Alterações**.

### <a name="create-logicmonitor-test-user"></a>Criar um usuário de teste do LogicMonitor

Para usuários do Microsoft Azure Active Directory conseguirem efetuar logon, eles devem ser provisionados para o aplicativo LogicMonitor usando seus nomes de usuário do Active Directory do Azure.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon no site da sua empresa LogicMonitor como um administrador.

2. No menu na parte superior, clique em **Configurações** e clique em **Funções e Usuários**.

    ![Funções e Usuários](./media/logicmonitor-tutorial/ic790056.png "Funções e Usuários")

3. Clique em **Adicionar**.

4. Na seção **Adicionar um conta**, execute as etapas a seguir:

    ![Adicionar uma conta](./media/logicmonitor-tutorial/ic790057.png "Adicionar uma conta")

    a. Digite os valores para **Nome de usuário**, **Email**, **Senha** e **Digitar senha novamente** do usuário do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

    b. Selecione **Funções**, **Exibir Permissões** e **Status**.

    c. Clique em **Enviar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do LogicMonitor ou APIs fornecidas pelo LogicMonitor para provisionar as contas de usuário do Active Directory do Azure.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do LogicMonitor, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do LogicMonitor e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do LogicMonitor em Meus Aplicativos, você será conectado automaticamente ao LogicMonitor, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o LogicMonitor, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
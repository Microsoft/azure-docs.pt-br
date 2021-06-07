---
title: 'Tutorial: Integração do Azure Active Directory ao Splunk Enterprise e Splunk Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Splunk Enterprise e o Splunk Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 81107b3655ae86d51e36445ce46661d553ab3b5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649845"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Tutorial: Integração do Azure Active Directory ao Splunk Enterprise e Splunk Cloud

Neste tutorial, você aprenderá a integrar o Splunk Enterprise e Splunk Cloud ao Azure AD (Azure Active Directory). Ao integrar o Splunk Enterprise e Splunk Cloud ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Splunk Enterprise e Splunk Cloud.
* Permitir que os usuários sejam conectados automaticamente ao Splunk Enterprise e Splunk Cloud com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Splunk Enterprise e Splunk Cloud habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Splunk Enterprise e Splunk Cloud dá suporte ao SSO iniciado por **SP**

## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Adicionar o Splunk Enterprise e o Splunk Cloud da galeria

Para configurar a integração do Splunk Enterprise e do Splunk Cloud com o Azure AD, você precisará adicionar o Splunk Enterprise e o Splunk Cloud à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Splunk Enterprise e Splunk Cloud** na caixa de pesquisa.
1. Selecione **Splunk Enterprise e Splunk Cloud** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Configurar e testar o SSO do Azure AD para o Splunk Enterprise e Splunk Cloud

Configure e teste o SSO do Azure AD com o Splunk Enterprise e Splunk Cloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Splunk Enterprise e Splunk Cloud.

Para configurar e testar o SSO do Azure AD com o Splunk Enterprise e Splunk Cloud, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Splunk Enterprise e Splunk Cloud](#configure-splunk-enterprise-and-splunk-cloud-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Splunk Enterprise e Splunk Cloud](#create-splunk-enterprise-and-splunk-cloud-test-user)** : para ter um equivalente de B.Fernandes no Splunk Enterprise e Splunk Cloud que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Splunk Enterprise e Splunk Cloud**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)
4. Na seção **Configuração Básica do SAML**, realize o seguinte padrão:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<splunkserverUrl>/app/launcher/home`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `<splunkserverUrl>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao Cliente do Splunk Enterprise e Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Splunk Enterprise e Splunk Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Splunk Enterprise e Splunk Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-splunk-enterprise-and-splunk-cloud-sso"></a>Configurar o SSO do Splunk Enterprise e Splunk Cloud

  Para configurar o logon único no lado do **Splunk Enterprise e Splunk Cloud**, você precisará enviar o **XML de Metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do Splunk Enterprise e Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Criar um usuário de teste do Splunk Enterprise e Splunk Cloud

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Splunk Enterprise e no Splunk Cloud. Trabalhe com a [equipe de suporte do Splunk Enterprise e do Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html) para adicionar os usuários à plataforma do Splunk Enterprise e do Splunk Cloud. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Splunk Enterprise e Splunk Cloud, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Splunk Enterprise e Splunk Cloud e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Splunk Enterprise e Splunk Cloud em Meus Aplicativos, você será redirecionado para a URL de Logon do Splunk Enterprise e Splunk Cloud. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Splunk Enterprise e Splunk Cloud, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)
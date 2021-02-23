---
title: 'Tutorial: Integração do Azure Active Directory ao ICIMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ICIMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/01/2021
ms.author: jeedes
ms.openlocfilehash: 5480e14ed31d8475bcb8d7bc40082bd979197afa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383833"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-icims"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ICIMS

Neste tutorial, você aprenderá a integrar o ICIMS ao Azure AD (Azure Active Directory). Ao integrar o ICIMS ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao ICIMS.
* Permitir que os usuários sejam conectados automaticamente ao ICIMS com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do ICIMS habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ICIMS dá suporte a SSO iniciado por **SP**

## <a name="add-icims-from-the-gallery"></a>Adicionar o ICIMS da galeria

Para configurar a integração do ICIMS ao Azure AD, você precisará adicionar o ICIMS da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ICIMS** na caixa de pesquisa.
1. Escolha **ICIMS** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-icims"></a>Configurar e testar o SSO do Azure AD para o ICIMS

Configure e teste o SSO do Azure AD com o ICIMS usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ICIMS.

Para configurar e testar o SSO do Azure AD com o ICIMS, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ICIMS](#configure-icims-sso)** : para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do ICIMS](#create-icims-test-user)** : para ter um equivalente de B.Fernandes no ICIMS que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **ICIMS**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant name>.icims.com`

    b. Na caixa de texto **Identificador**, digite a URL `icims.com`

    c. Na caixa de texto **URL de Resposta**, digite a URL: `https://.icims.com/`

    > [!NOTE]
    > O valor da URL de Entrada não é real. Atualize esse valor com a URL de logon real. Entre em contato com a [equipe de suporte ao cliente do ICIMS](https://www.icims.com/contact-us) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o ICIMS**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao ICIMS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **ICIMS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-icims-sso"></a>Configurar o SSO do ICIMS

Para configurar o logon único no lado do **ICIMS**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do ICIMS](https://www.icims.com/contact-us). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-icims-test-user"></a>Criar um usuário de teste do ICIMS

Nesta seção, você criará um usuário chamado B.Fernandes no ICIMS. Trabalhe com a [equipe de suporte do ICIMS](https://www.icims.com/contact-us) para adicionar os usuários à plataforma ICIMS. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do ICIMS, na qual será possível iniciar o fluxo de logon. 

* Acesse a URL de Logon do ICIMS diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do ICIMS em Meus Aplicativos, você será redirecionado à URL de Logon do ICIMS. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o ICIMS, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao SendPro Enterprise | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o SendPro Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 2acb234d04a0158b22b9078db903e6be562b892b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954659"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sendpro-enterprise"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SendPro Enterprise

Neste tutorial, você aprenderá a integrar o SendPro Enterprise ao Azure AD (Azure Active Directory). Ao integrar o SendPro Enterprise ao Azure AD, será possível:

* Controlar quem tem acesso ao SendPro Enterprise no Azure AD.
* Permitir que os usuários sejam conectados ao SendPro Enterprise de modo automático usando as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SendPro Enterprise.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SendPro Enterprise é compatível com SSO iniciado por **SP**

## <a name="adding-sendpro-enterprise-from-the-gallery"></a>Como adicionar o SendPro Enterprise por meio da galeria

Será necessário adicionar o SendPro Enterprise por meio da galeria à sua lista de aplicativos SaaS gerenciados para configurar a integração dele com o Azure AD.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SendPro Enterprise** na caixa de pesquisa.
1. Selecione **SendPro Enterprise** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-sendpro-enterprise"></a>Configurar e testar o SSO do Azure AD para o SendPro Enterprise

Configure e teste o SSO do Azure AD usando o SendPro Enterprise com uma usuária de teste chamada **B.Fernandes**. Será necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SendPro Enterprise para que o SSO funcione.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o SendPro Enterprise:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SendPro Enterprise](#configure-sendpro-enterprise-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SendPro Enterprise](#create-sendpro-enterprise-test-user)** – Para ter um equivalente a B.Fernandes no SendPro Enterprise que esteja vinculado à declaração de um usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SendPro Enterprise**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<TENANT_NAME>.sendproenterprise.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do SendPro Enterprise](https://www.pitneybowes.com/us/support.html) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o SendPro Enterprise**, copie as URLs apropriadas de acordo com os seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use um logon único do Azure, permitindo o acesso ao SendPro Enterprise.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SendPro Enterprise**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sendpro-enterprise-sso"></a>Configurar um SSO do SendPro Enterprise

Para configurar um logon único no lado do **SendPro Enterprise**, será necessário enviar o **XML de Metadados Federados** baixado, além de URLs apropriadas e copiadas do portal do Azure para a [equipe de suporte do SendPro Enterprise](https://www.pitneybowes.com/us/support.html). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-sendpro-enterprise-test-user"></a>Criar um usuário de teste do SendPro Enterprise

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SendPro Enterprise. Trabalhe com a [equipe de suporte do SendPro Enterprise](https://www.pitneybowes.com/us/support.html) para adicionar usuários à plataforma do SendPro Enterprise. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do SendPro Enterprise, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do SendPro Enterprise e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do SendPro Enterprise em Meus Aplicativos, isso o redirecionará para a URL de logon do SendPro Enterprise. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SendPro Enterprise, será possível impor um controle de sessão que fornecerá proteção contra a exfiltração e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
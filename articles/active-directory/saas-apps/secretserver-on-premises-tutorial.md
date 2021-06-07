---
title: 'Tutorial: Integração do Azure Active Directory ao Secret Server (On-Premises)| Microsoft Docs'
description: Saiba como configurar logon único entre o Azure Active Directory e o Servidor de Segredo (local).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d723bf1ce82e6d443dfa55dda7d33a3a9bfc16b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647006"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Tutorial: Integrar o Secret Server (On-Premises) ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Secret Server (On-Premises) ao Microsoft Azure AD (Azure Active Directory). Ao integrar o Secret Server (On-Premises) ao Azure AD, você pode:

* Controlar no Microsoft Azure AD quem tem acesso ao Secret Server (On-Premises).
* Permitir que os usuários sejam conectados automaticamente ao Secret Server (On-Premises) com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Secret Server (On-Premises).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Secret Server (On-Premises) é compatível com SSO iniciado por **SP e DIP**

## <a name="add-secret-server-on-premises-from-the-gallery"></a>Adicionar o Secret Server (On-Premises) por meio da galeria

Para configurar a integração do Servidor de Segredo (local) ao Microsoft Azure AD, você precisa adicionar o Servidor de Segredo (local) da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Secret Server (On-Premises)** caixa de pesquisa.
1. Selecione **Secret Server (On-Premises)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-secret-server-on-premises"></a>Configurar e testar o SSO do Azure AD para o Secret Server (On-Premises)

Configure e teste o SSO do Azure AD com o Secret Server (On-Premises) usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Secret Server (On-Premises).

Para configurar e testar o SSO do Azure AD com o Secret Server (On-Premises), execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Secret Server (On-Premises)](#configure-secret-server-on-premises-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Servidor de Segredo (local)](#create-secret-server-on-premises-test-user)** – para ter um equivalente de B. Fernandes no Secret Server (On-Premises) vinculado à representação do usuário no Microsoft Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Secret Server (On-Premises)** , localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite a URL `https://secretserveronpremises.azure`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > A ID da Entidade mostrada acima é apenas um exemplo e você é livre para escolher qualquer valor exclusivo que identifique a instância do Servidor de Segredo no Microsoft Azure AD. É necessário enviar essa ID da Entidade à [Equipe de suporte ao Cliente do Servidor de Segredo (local)](https://thycotic.force.com/support/s/) para que possam configurá-lo. Para obter mais detalhes, leia[este artigo](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [Equipe de suporte ao Cliente do Servidor de Segredo (local)](https://thycotic.force.com/support/s/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na página **Configurar Logon Único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Captura de tela que mostra a seção "Certificado de Autenticação SAML" com a ação "Baixar" do Certificado (Base64) selecionada.](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Selecione **Opção de Assinatura** como **Assinar resposta SAML e declaração**.

    ![Opções de assinatura](./media/secretserver-on-premises-tutorial/signing-option.png)

1. Na seção **Configurar Secret Server (On-Premises)** , copie as URLs apropriadas com base nas suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder a ela o acesso ao Secret Server (On-Premises).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Servidor de Segredo (local)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-secret-server-on-premises-sso"></a>Definir SSO do Secret Server (On-Premises)

Para configurar o logon único no lado do **Secret Server (On-Premises)** , envie o **Certificado (Base64)** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-secret-server-on-premises-test-user"></a>Criar usuário de teste do Secret Server (On-Premises)

Nesta seção, você cria um usuário chamado Brenda Fernandes no Servidor de Segredo (local). Trabalhe com a [Equipe de suporte do Servidor de Segredo (local)](https://thycotic.force.com/support/s/) para adicionar os usuários na plataforma do Servidor de Segredo (local). Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Secret Server (On-Premises), na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do Secret Server (On-Premises) diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Secret Server (On-Premises), para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Secret Server (On-Premises) em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Secret Server (On-Premises) para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Secret Server (On-Premises), você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
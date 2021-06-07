---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Pulse Secure PCS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Pulse Secure PCS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d38ff5c8f33bda5b12f6267e7a8cdf477db6c7d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511436"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Pulse Secure PCS

Neste tutorial, você aprenderá a integrar o Pulse Secure PCS ao Azure AD (Azure Active Directory). Ao integrar o PCS Pulse Secure ao Azure AD, você pode:

* Controlar no Azure AD que tem acesso ao Pulse Secure PCS.
* Permitir que os usuários, com as respectivas contas do Azure AD, sejam conectados automaticamente ao Pulse Secure PCS.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Pulse Secure PCS com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Pulse Secure PCS dá suporte a SSO iniciado por **SP**

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Como adicionar o Pulse Secure PCS da galeria

Para configurar a integração do Pulse Secure PCS ao Azure AD, é necessário adicionar o Pulse Secure PCS da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Pulse Secure PCS** na caixa de pesquisa.
1. Selecione **Pulse Secure PCS** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Configurar e testar o SSO do Azure AD para PCS Pulse Secure

Configure e teste o SSO do Azure AD com o Pulse Secure PCS usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Pulse Secure PCS.

Para configurar e testar o SSO do Azure AD com o Pulse Secure PCS, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Pulse Secure PCS](#configure-pulse-secure-pcs-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Pulse Secure PCS](#create-pulse-secure-pcs-test-user)** – para ter um equivalente de B.Fernandes no Pulse Secure PCS vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Pulse Secure PCS**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, a URL de Resposta e o Identificador reais. Contate a [equipe de suporte ao cliente do Pulse Secure PCS](mailto:support@pulsesecure.net) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Pulse Secure PCS**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Pulse Secure PCS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Pulse Secure PCS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-pulse-secure-pcs-sso"></a>Configurar o SSO do Pulse Secure PCS

Esta seção aborda as configurações de SAML necessárias para configurar o PCS como o SP do SAML. As outras configurações básicas, como a criação de territórios e funções, não são cobertas.

**As configurações do Pulse Secure PCS incluem:**

* Configurar o Azure AD como provedor de metadados SAML
* Configurar o Servidor de Autenticação SAML
* Atribuir aos respectivos territórios e funções

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Configurar o Azure AD como provedor de metadados SAML

Execute as etapas abaixo na seguinte página:

![Configuração do Pulse Secure PCS](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Faça logon no console de administração do Pulse Connect Secure
1. Navegue até **Sistema -> Configuração -> SAML**
1. Clique em **Novo Provedor de Metadados**
1. Forneça o nome válido na caixa de texto **Nome**
1. Carregue o arquivo XML de metadados baixado do portal do Azure para o **arquivo de metadados do Azure AD**.
1. Selecionar **Aceitar Metadados Não Assinados**
1. Selecionar Funções como **Provedor de Identidade**
1. Clique em **Salvar alterações**.

#### <a name="steps-to-create-a-saml-auth-server"></a>Etapas para criar um servidor de autenticação SAML:

1. Navegue até **Autenticação-> Servidores de Autenticação**
1. Selecione **Novo: Servidor SAML** e clique em **Novo Servidor**

    ![Servidor de autenticação do Pulse Connect Secure](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. Execute as seguintes etapas na página de configurações:

    ![Configurações do servidor de autenticação do Pulse Connect Secure](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Forneça o **Nome do Servidor** na caixa de texto.

    b. Selecione **SAML versão 2.0** e **Modo de Configuração** como **Metadados**.

    c. Copie o valor da **ID da Entidade do Connect Secure** e cole-o na caixa **URL do Identificador** da caixa de diálogo **Configuração Básica do SAML** no portal do Azure.

    d. Selecione o valor da ID de entidade do Azure AD na **lista suspensa ID de Entidade do Provedor de Identidade**.

    e. Selecione o valor da URL de logon do Azure AD na **lista suspensa URL do Serviço de Logon Único do Provedor de Identidade**.

    f. **Logoff Único** é uma configuração opcional. Se essa opção for selecionada, ela solicitará uma nova autenticação após o logoff. Se essa opção não estiver selecionada e você não tiver fechado o navegador, você poderá se reconectar sem autenticação.

    g. Selecione **Classe de Contexto de Authn Solicitada** como **Senha** e o **Método de Comparação** como **exato**.

    h. Defina a **Validade dos Metadados** em termos de número de dias.
    
    i. Clique em **salvar alterações**

### <a name="create-pulse-secure-pcs-test-user"></a>Criar o usuário de teste do Pulse Secure PCS

Nesta seção, você criará um usuário chamado Brenda Fernandes no Pulse Secure PCS. Trabalhe com a [equipe de suporte do Pulse Secure PCS](mailto:support@pulsesecure.net) para adicionar os usuários na plataforma do Pulse Secure PCS. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Pulse Secure PCS, na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de logon do Pulse Secure PCS diretamente e inicie o fluxo de logon de lá.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco Pulse Secure PCS no painel de acesso, você será redirecionado para a URL de logon do Pulse Secure PCS. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Pulse Secure PCS, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do Azure Active Directory ao SAP Qualtrics | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Qualtrics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 2031864ea57a2f061c69219a2382429ee035804b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652533"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SAP Qualtrics

Neste tutorial, você aprenderá a integrar o SAP Qualtrics ao Azure AD (Azure Active Directory). Ao integrar o SAP Qualtrics ao Azure AD, você pode:

* Controlar no Azure AD que tem acesso ao SAP Qualtrics.
* Permitir que os usuários, com as respectivas contas do Azure AD, sejam conectados automaticamente ao SAP Qualtrics.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do SAP Qualtrics habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SAP Qualtrics é compatível com o SSO iniciado por **SP** e **IDP**.
* O SAP Qualtrics é compatível com o provisionamento de usuário **Just-In-Time**.

## <a name="add-sap-qualtrics-from-the-gallery"></a>Adicionar o SAP Qualtrics da galeria

Para configurar a integração do SAP Qualtrics ao Azure AD, é necessário adicionar o SAP Qualtrics da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SAP Qualtrics** na caixa de pesquisa.
1. Selecione **SAP Qualtrics** nos resultados e depois adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Configurar e testar o logon único do Azure AD para o SAP Qualtrics

Configure e teste o SSO do Azure AD com o SAP Qualtrics usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Qualtrics.

Para configurar e testar o SSO do Azure AD com o SAP Qualtrics, conclua os seguintes blocos de construção:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
1. [Configurar o SSO do SAP Qualtrics](#configure-sap-qualtrics-sso) para definir as configurações de logon único no lado do aplicativo.
    1. [Crie um usuário de teste do SAP Qualtrics](#create-sap-qualtrics-test-user) para ter um equivalente de B.Fernandes no SAP Qualtrics que esteja vinculado à representação do usuário do Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SAP Qualtrics**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, caso queira configurar o aplicativo no modo iniciado por **IDP**, insira os valores nos seguintes campos:
    
    a. Na caixa de texto **Identificador**, digite uma URL que usa o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com`
   
    b. Na caixa de texto **URL de Resposta**, digite uma URL que use o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Na caixa de texto **Estado de Retransmissão**, digite uma URL que use o seguinte padrão:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Selecione **Definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado por **SP**:

    Na caixa de texto **URL de Entrada**, digite uma URL que usa o seguinte padrão:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, o Identificador, a URL de Resposta e o Estado de Retransmissão reais. Para obter esses valores, contate a [equipe de suporte do cliente do Qualtrics](https://www.qualtrics.com/support/). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o ícone copiar para copiar a **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de seleção **Mostrar senha** e, em seguida, anote a senha.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá a B.Fernandes acesso ao SAP Qualtrics, que permitirá o uso do logon único do Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Qualtrics**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-sap-qualtrics-sso"></a>Configurar o SSO do SAP Qualtrics

Para configurar o logon único no lado do SAP Qualtrics, envie a **URL de Metadados de Federação do Aplicativo** copiada do portal do Azure para a [equipe de suporte do SAP Qualtrics](https://www.qualtrics.com/support/). Essa equipe garante que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-sap-qualtrics-test-user"></a>Criar um usuário de teste do SAP Qualtrics

O SAP Qualtrics é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há nenhuma ação para você executar. Se um usuário ainda não existir no SAP Qualtrics, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do SAP Qualtrics, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do SAP Qualtrics e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao SAP Qualtrics, para o qual configurou o SSO.

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do SAP Qualtrics em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao SAP Qualtrics, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o SAP Qualtrics, você poderá impor o controle de sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. Para obter mais informações, confira [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integrar o SSO (logon único) do Azure Active Directory ao Splan Visitor | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Splan Visitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: 20f49c174dde90bc7f1a9b34f3dea3132e9b177e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644673"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Tutorial: Integrar o SSO (logon único) do Azure Active Directory ao Splan Visitor

Neste tutorial, você aprenderá a integrar o Splan Visitor ao Azure AD (Azure Active Directory). Ao integrar o Splan Visitor ao Azure AD, você poderá:

* Usar o Azure AD para controlar quem tem acesso ao Splan Visitor.
* Permitir que os usuários sejam conectados automaticamente ao Splan Visitor com as respectivas contas do Azure AD.
* gerenciar suas contas em um local central, o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Splan Visitor habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

O Splan Visitor dá suporte ao SSO iniciado por IdP.

## <a name="add-splan-visitor-from-the-gallery"></a>Como adicionar o Splan Visitor por meio da galeria

Para configurar a integração do Splan Visitor ao Azure AD, adicione o Splan Visitor por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel esquerdo, selecione o serviço **Microsoft Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, insira **Splan Visitor** na caixa de pesquisa.
1. Selecione **Splan Visitor** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Configurar e testar o SSO do Azure AD para o Splan Visitor

Configure e teste o SSO do Azure AD com o Splan Visitor usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Splan Visitor.

Para configurar e testar o SSO do Azure AD com o Splan Visitor, execute as seguintes etapas:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com o usuário de teste B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Splan Visitor](#configure-splan-visitor-sso)** para definir as configurações de logon único no Splan Visitor.
    1. **[Criar um usuário de teste do Splan Visitor](#create-a-splan-visitor-test-user)** para ter um equivalente de B.Fernandes no Splan Visitor que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. No portal do Azure, na página de integração de aplicativos do **Splan Visitor**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, selecione o ícone **editar/caneta** de **Configuração Básica do SAML** para editar as configurações.

   ![Captura de tela que realça o ícone editar/caneta de Configuração Básica do SAML.](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o aplicativo é pré-configurado e as URLs necessárias são preenchidas previamente com o Azure. Clique no botão **Salvar** para salvar a configuração.

1. Na página **Configurar Logon Único com o SAML** na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação**. Selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![Captura de tela que realça o link de download do XML de Metadados de Federação.](common/metadataxml.png)

1. Na seção **Configurar o Splan Visitor**, copie as URLs apropriadas de acordo com as suas necessidades.

    ![Captura de tela que realça a seção URLs de configuração.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory**, **Usuários** e **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira **B.Fernandes**.  
   1. No campo **Nome de usuário**, insira o nome de usuário no formato _username@companydomain.extension_ . Por exemplo, insira **B.Simon@contoso.com** .
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Splan Visitor.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Splan Visitor** para abrir a visão geral do aplicativo.
1. Localize a seção **Gerenciar** e selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários** e clique em **Selecionar** na parte inferior da tela.
1. Se o usuário receber uma função, escolha-a no menu suspenso **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, mantenha a função **Acesso Padrão** selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-splan-visitor-sso"></a>Configurar o SSO do Splan Visitor

Para configurar o logon único com o Splan Visitor, envie o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Splan Visitor](mailto:support@splan.com). Isso garante que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-a-splan-visitor-test-user"></a>Criar um usuário de teste do Splan Visitor

Crie um usuário de teste chamado **Brenda Fernandes** no Splan Visitor. Trabalhe com a [equipe de suporte do Splan Visitor](mailto:support@splan.com) para adicionar o usuário ao Splan Visitor. Você precisará criar e ativar o usuário antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Teste sua configuração de logon único do Azure AD com uma das seguintes opções:

* **Portal do Azure**: Selecione **Testar este aplicativo** para entrar automaticamente no Splan Visitor, para o qual você configurou o SSO.
* **Portal Meus Aplicativos da Microsoft**: Selecione o bloco do **Splan Visitor** para entrar automaticamente no Splan Visitor, para o qual você configurou o SSO. Para obter mais informações sobre o portal Meus Aplicativos, confira [Entrar e iniciar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Splan Visitor, [saiba como impor controles de sessão no Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app). Os controles de sessão ajudam a fornecer proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional.
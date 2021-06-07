---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Single Sign-on for Skytap | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Single Sign-on for Skytap.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.openlocfilehash: 5577b0cb480b372b64a9cb25f52617edfca3f2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98108119"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Single Sign-on for Skytap

Neste tutorial, você aprenderá a integrar o Single Sign-on for Skytap ao Azure AD (Azure Active Directory). Ao integrar o Single Sign-on for Skytap ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Single Sign-on for Skytap.
* Permitir que os usuários sejam conectados automaticamente ao Single Sign-on for Skytap com as respectivas contas do Azure AD.
* gerenciar suas contas em um local central, o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Single Sign-on for Skytap.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Single Sign-on for Skytap dá suporte ao SSO iniciado por SP e IdP.
* Após configurar o Single Sign-on for Skytap, você poderá impor o controle de sessão. Isso protege contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Adicionar o Single Sign-on for Skytap por meio da galeria

Para configurar a integração do Single Sign-on for Skytap ao Azure AD, você precisará adicionar o Single Sign-on for Skytap por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Acesse **Aplicativos Empresariais** e, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Single Sign-on for Skytap** na caixa de pesquisa.
1. Selecione **Single Sign-on for Skytap** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Configurar e testar o logon único do Azure AD para o Single Sign-on for Skytap

Configure e teste o SSO do Azure AD com o Single Sign-on for Skytap usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Single Sign-on for Skytap.

Aqui estão as etapas gerais para configurar e testar o SSO do Azure AD com o Single Sign-on for Skytap:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.

    a. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.

    b. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Single Sign-on for Skytap](#configure-single-sign-on-for-skytap-sso)** para definir as configurações de logon único no lado do aplicativo.

    a. **[Criar um usuário de teste do Single Sign-on for Skytap](#create-single-sign-on-for-skytap-test-user)** para ter um equivalente de B.Fernandes no Single Sign-on for Skytap. Esse equivalente é vinculado à representação do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Single Sign-on for Skytap**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o logon único com SAML, com o ícone de lápis realçado](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IdP**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL que usa o seguinte padrão: `http://pingone.com/<custom EntityID>`

    b. Na caixa de texto **URL de Resposta**, digite uma URL que use o seguinte padrão: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Opcionalmente, selecione **Definir URLs adicionais** e execute as seguintes etapas para configurar o aplicativo no modo iniciado por **SP**:

    a. Na caixa de texto **URL de Entrada**, digite uma URL que usa o seguinte padrão: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. Na caixa de texto **Estado de Retransmissão**, digite uma URL que use o seguinte padrão: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta, a URL de Logon e o Estado de retransmissão reais. Entre em contato com a [equipe de suporte ao cliente do Single Sign-on for Skytap](mailto:support@skytap.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação**. Selecione **Baixar** para baixar o arquivo de metadados e salvá-lo em seu computador.

    ![Captura de tela do link de download do certificado](common/metadataxml.png)

1. Na seção **Configurar o Single Sign-on for Skytap**, copie a URL ou URLs apropriadas de acordo com as suas necessidades.

    ![Captura de tela de copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor mostrado no campo **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Single Sign-on for Skytap.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Single Sign-on for Skytap**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com Usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Captura de tela da página Usuários e grupos, com a função Adicionar usuário destacada](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Configurar o SSO do Single Sign-on for Skytap

Para configurar o logon único no lado do Single Sign-on for Skytap, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte ao cliente do Single Sign-on for Skytap](mailto:support@skytap.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Criar um usuário de teste do Single Sign-on for Skytap

Nesta seção, você criará uma usuária chamada B.Fernandes no Single Sign-on for Skytap. Trabalhe com a [equipe de suporte ao cliente do Single Sign-on for Skytap](mailto:support@skytap.com) para adicionar os usuários na plataforma do Single Sign-on for Skytap. Você não pode usar o logon único antes de criar e ativar usuários.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Single Sign-on for Skytap no Painel de Acesso, você deverá ser conectado automaticamente ao Single Sign-on for Skytap para o qual configurou o SSO. Para obter mais informações, confira [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Slack com o Azure AD](https://aad.portal.azure.com/)

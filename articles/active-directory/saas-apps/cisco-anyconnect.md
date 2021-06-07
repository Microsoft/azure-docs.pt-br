---
title: 'Tutorial: Integração SSO (logon único) do Azure Active Directory com o Cisco AnyConnect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco AnyConnect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: a89ab7f2304fa51d3e8c7a968d445c9b40a457a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456081"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-anyconnect"></a>Tutorial: Integração SSO (logon único) do Azure Active Directory com o Cisco AnyConnect

Neste tutorial, você aprenderá a integrar o Cisco AnyConnect ao Azure AD (Azure Active Directory). Com a integração do Cisco AnyConnect ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Cisco AnyConnect.
* Permitir que os usuários sejam conectados automaticamente ao Cisco AnyConnect com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Cisco AnyConnect.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cisco AnyConnect é compatível com SSO iniciado por **IDP**

## <a name="adding-cisco-anyconnect-from-the-gallery"></a>Como adicionar o Cisco AnyConnect da galeria

Para configurar a integração do Cisco AnyConnect com o Azure AD, você precisa adicionar o Cisco AnyConnect da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **Cisco AnyConnect** na caixa de pesquisa.
1. Selecione **Cisco AnyConnect** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-cisco-anyconnect"></a>Configurar e testar o SSO do Azure AD para o Cisco AnyConnect

Configure e teste o SSO do Azure AD com o Cisco AnyConnect usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cisco AnyConnect.

Para configurar e testar o SSO do Azure AD com o Cisco AnyConnect, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Cisco AnyConnect](#configure-cisco-anyconnect-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Cisco AnyConnect](#create-cisco-anyconnect-test-user)** para ter um equivalente de B.Fernandes no Cisco AnyConnect que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Cisco AnyConnect**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `< YOUR CISCO ANYCONNECT VPN VALUE >`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do Cisco AnyConnect](https://www.cisco.com/c/en/us/support/index.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o arquivo de certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Cisco AnyConnect**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

> [!NOTE]
> Se você quiser integrar vários TGTs do servidor, precisará adicionar várias instâncias do aplicativo Cisco AnyConnect por meio da galeria. Além disso, você pode optar por carregar seu certificado no Azure AD para todas essas instâncias de aplicativo. Dessa forma, você pode ter o mesmo certificado para os aplicativos, mas pode configurar Identificador e URL de Resposta diferentes para cada aplicativo.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Cisco AnyConnect.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Cisco AnyConnect**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cisco-anyconnect-sso"></a>Configurar o SSO do Cisco AnyConnect

1. Você vai fazer isso na CLI primeiro. Você pode voltar e fazer um passo a passo do ASDM em outro momento.

1. Conecte-se ao seu dispositivo de VPN. Você usará um ASA executando o treinamento de código 9.8 e seus clientes VPN serão 4.6+.

1. Primeiro, você criará um Trustpoint e importará nosso certificado SAML.

   ```
    config t

    crypto ca trustpoint AzureAD-AC-SAML
      revocation-check none
      no id-usage
      enrollment terminal
      no ca-check
    crypto ca authenticate AzureAD-AC-SAML
    -----BEGIN CERTIFICATE-----
    …
    PEM Certificate Text from download goes here
    …
    -----END CERTIFICATE-----
    quit
   ```

1. Os comandos a seguir provisionarão o IdP do SAML.

   ```
    webvpn
    saml idp https://sts.windows.net/xxxxxxxxxxxxx/ (This is your Azure AD Identifier from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-in https://login.microsoftonline.com/xxxxxxxxxxxxxxxxxxxxxx/saml2 (This is your Login URL from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-out https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 (This is Logout URL from the Set up Cisco AnyConnect section in the Azure portal)
    trustpoint idp AzureAD-AC-SAML
    trustpoint sp (Trustpoint for SAML Requests - you can use your existing external cert here)
    no force re-authentication
    no signature
    base-url https://my.asa.com
    ```

1. Agora você pode aplicar a autenticação SAML a uma configuração de túnel VPN.

    ```
    tunnel-group AC-SAML webvpn-attributes
      saml identity-provider https://sts.windows.net/xxxxxxxxxxxxx/
      authentication saml
    end

    write mem
    ```

    > [!NOTE]
    > Há um recurso com a configuração IdP do SAML. Se você fizer alterações à configuração do IdP, será necessário remover a configuração do provedor de identidade SAML do seu grupo de túnel e reaplicá-la para que as alterações entrem em vigor.

### <a name="create-cisco-anyconnect-test-user"></a>Criar usuário de teste do Cisco AnyConnect

Nesta seção, você criará um usuário chamado Brenda Fernandes no Cisco AnyConnect. Trabalhe com a [equipe de suporte do Cisco AnyConnect](https://www.cisco.com/c/en/us/support/index.html) para adicionar os usuários na plataforma Cisco AnyConnect. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será automaticamente conectado ao Cisco AnyConnect para o qual configurou o SSO
* Use o Painel de Acesso da Microsoft. Ao clicar no bloco do Cisco AnyConnect no Painel de Acesso, você deverá ser conectado automaticamente ao Cisco AnyConnect para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Cisco AnyConnect, você poderá impor o controle de sessão, que fornece proteção contra a exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
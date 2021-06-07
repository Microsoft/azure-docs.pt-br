---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao RStudio Server Pro | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RStudio Server Pro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 490ecb201b91cdbdcdddceecdd2d145d2f9bb815
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390038"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rstudio-server-pro"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao RStudio Server Pro

Neste tutorial, você aprenderá a integrar o RSP (RStudio Server Pro) ao Azure AD (Azure Active Directory). Ao integrar o RSP ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao RSP.
* Permitir que os usuários sejam conectados automaticamente ao RSP com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Instalação do RSP (versão >= 1.4).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O RSP dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-rstudio-server-pro-from-the-gallery"></a>Como adicionar o RStudio Server Pro da galeria

Para configurar a integração do RSP ao Azure AD, você precisa adicionar a autenticação SAML do RStudio Server Pro por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **RStudio Server Pro SAML Authentication** na caixa de pesquisa.
1. Selecione **RStudio Server Pro SAML Authentication** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-server-pro"></a>Configurar e testar o SSO do Azure AD para o RStudio Server Pro

Configure e teste o SSO do Azure AD com o RSP usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do RSP.

Para configurar e testar o SSO do Azure AD com o RStudio Server Pro SAML Authentication, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do RStudio Server Pro](#configure-rstudio-server-pro-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar de um usuário de teste do RStudio Server Pro](#create-rstudio-server-pro-test-user)** : para ter um equivalente de B.Fernandes no RStudio Server Pro que esteja vinculado à representação de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **RStudio Server Pro SAML Authentication**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<RSP-SERVER>/<PATH>/saml/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<RSP-SERVER>/<PATH>/saml/acs`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<RSP-SERVER>/<PATH>/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o URI real da sua instalação do RSP. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao RStudio Server Pro SAML Authentication.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **RStudio Server Pro SAML Authentication**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-rstudio-server-pro-sso"></a>Configurar o SSO do RStudio Server Pro

1. Atualize o arquivo de configuração do RSP `/etc/rstudio/rserver.conf` com o seguinte:

    ```
    auth-saml=1
    auth-saml-metadata-url=<federation-metadata-URI>
    auth-saml-sp-name-id-format=emailaddress
    auth-saml-sp-attribute-username=NameID
    auth-saml-sp-base-uri=<RSP-Server-URI>
    ```

2. Reinicie o RSP executando o seguinte:

    ```
    sudo rstudio-server restart
    ```

### <a name="create-rstudio-server-pro-test-user"></a>Criar um usuário de teste do RStudio Server Pro

Todos os usuários que devem usar o RSP precisam ser provisionados no servidor. Você pode criar o usuário com o comando `useradd` ou `adduser`.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do RStudio Server Pro SAML Authentication, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do RStudio Server Pro SAML Authentication, na qual você iniciará o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao RStudio Server Pro SAML Authentication para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do RStudio Server Pro SAML Authentication no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao RStudio Server Pro SAML Authentication para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o RStudio Server Pro SAML Authentication, você poderá impor o controle de sessão, que protege contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o E-days | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o E-days.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: 3116b9f4f8311ff4904795b1b9c27d5fe81dcb2f
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736581"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-e-days"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o E-days

Neste tutorial, você aprenderá a integrar o E-days com o Azure AD (Azure Active Directory). Ao integrar o E-days com o Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao E-days.
* Permitir que os usuários sejam conectados automaticamente ao E-days com as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do E-days.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O E-days é compatível com SSO iniciado por **SP e IDP**


## <a name="adding-e-days-from-the-gallery"></a>Adicionando o E-days por meio da galeria

Para configurar a integração do E-days ao Azure AD, você precisa adicionar o E-days por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **E-days** na caixa de pesquisa.
1. Selecione **E-days** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-e-days"></a>Configurar e testar o SSO do Azure AD para o E-days

Configure e teste o SSO do Azure AD com o E-days usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do E-days.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o E-days:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do E-days](#configure-e-days-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do E-days](#create-e-days-test-user)** – para ter um equivalente de B.Fernandes no E-days que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **E-days**, localize a seção **Gerenciar** e escolha **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    `https://<SUBDOMAIN>.e-days.com`

    b. Na caixa de texto **URL de Resposta**, digite uma URL nos seguintes padrões:

    | URL de resposta |
    |-------------|
    | `https://<SUBDOMAIN>.e-days.com/SSO/SAML2/SP/AssertionConsumer.aspx` |
    | `https://<SUBDOMAIN>.signin.e-days.com/<CUSTOM_URL>` |
    | `https://<SUBDOMAIN>.signin.e-days.co.uk/<CUSTOM_URL>` |
    |


1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:

    `https://<SUBDOMAIN>.e-days.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do cliente E-days](https://support.e-days.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o E-days**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao E-days.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **E-days**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-e-days-sso"></a>Configurar o SSO do E-Days

Para configurar o logon único no lado do **E-days**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do E-days](https://support.e-days.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-e-days-test-user"></a>Criar usuário de teste do E-Days

Nesta seção, você criará um usuário com o nome B.Fernandes no E-days. Trabalhe com a [equipe de suporte do E-days](https://support.e-days.com) para adicionar os usuários na plataforma do E-days. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do E-days, na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do E-days diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você deverá entrar automaticamente no E-days, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do E-days no portal Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao E-days para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o E-days, você poderá impor controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do Azure Active Directory à Configuração de SSO do Azure AD do MVISION Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a Configuração de SSO do Azure AD do MVISION Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: eaf6b2526125b13eec67680c292ed1dbae6fcfee
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284389"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Tutorial: Integrar a Configuração de SSO do Azure AD do MVISION Cloud ao Azure Active Directory

Neste tutorial, você aprenderá a integrar a Configuração de SSO do Azure AD do MVISION Cloud ao Azure AD (Azure Active Directory). Ao integrar a Configuração de SSO do Azure AD do MVISION Cloud ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso à Configuração de SSO do Azure AD do MVISION Cloud.
* Permitir que os usuários sejam conectados automaticamente à Configuração de SSO do Azure AD do MVISION Cloud com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura da Configuração de SSO do Azure AD do MVISION Cloud habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A Configuração de SSO do Azure AD do MVISION Cloud dá suporte ao SSO iniciado por **SP e IdP**.

## <a name="add-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Adicionar a Configuração de SSO do Azure AD do MVISION Cloud por meio da galeria

Para configurar a integração da Configuração de SSO do Azure AD do MVISION Cloud ao Azure AD, você precisará adicionar a Configuração de SSO do Azure AD do MVISION Cloud por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Configuração de SSO do Azure AD do MVISION Cloud** na caixa de pesquisa.
1. Selecione **Configuração de SSO do Azure AD do MVISION Cloud** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-mvision-cloud-azure-ad-sso-configuration"></a>Configurar e testar o SSO do Azure AD para a Configuração de SSO do Azure AD do MVISION Cloud

Configure e teste o SSO do Azure AD com a Configuração de SSO do Azure AD do MVISION Cloud usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na Configuração de SSO do Azure AD do MVISION Cloud.

Para configurar e testar o SSO do Azure AD com a Configuração de SSO do Azure AD do MVISION Cloud, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO da Configuração de SSO do Azure AD do MVISION Cloud](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste da Configuração de SSO do Azure AD do MVISION Cloud](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** – para ter um equivalente de Brenda Fernandes na Configuração de SSO do Azure AD do MVISION Cloud que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Datadog**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente da Configuração de SSO do Azure AD do MVISION Cloud](mailto:support@skyhighnetworks.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Definir a Configuração de SSO do Azure AD do MVISION Cloud**, copie as URLs apropriadas de acordo com seus requisitos.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure AD permitindo acesso à Configuração de SSO do Azure AD do MVISION Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Configuração de SSO do Azure AD do MVISION Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Configurar o SSO na Configuração de SSO do Azure AD do MVISION Cloud

Para configurar o logon único no lado da **Configuração de SSO do Azure AD do MVISION Cloud**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte da Configuração de SSO do Azure AD do MVISION Cloud](mailto:support@skyhighnetworks.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Criar um usuário de teste da Configuração de SSO do Azure AD do MVISION Cloud

Nesta seção, você criará um usuário chamado B.Fernandes na Configuração de SSO do Azure AD do MVISION Cloud. Trabalhe com a [equipe de suporte da Configuração de SSO do Azure AD do MVISION Cloud](mailto:support@skyhighnetworks.com) para adicionar os usuários na plataforma da Configuração de SSO do Azure AD do MVISION Cloud. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon da Configuração de SSO do Azure AD do MVISION Cloud, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon da Configuração de SSO do Azure AD do MVISION Cloud e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente na Configuração de SSO do Azure AD do MVISION Cloud, para a qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco da Configuração de SSO do Azure AD do MVISION Cloud em Meus Aplicativos, se ela estiver configurada no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ela estiver configurada no modo IdP, você entrará automaticamente na Configuração de SSO do Azure AD do MVISION Cloud, para a qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar a Configuração de SSO do Azure AD do MVISION Cloud, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

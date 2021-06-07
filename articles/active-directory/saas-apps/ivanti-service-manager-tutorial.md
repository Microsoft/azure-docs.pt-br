---
title: 'Tutorial: integração do Azure Active Directory com o Ivanti Service Manager (ISM)|Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Ivanti Service Manager (ISM).
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
ms.openlocfilehash: 16aceeddcba574953ac03ccf04d099762b0000c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648876"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Tutorial: integração do Azure Active Directory com o Ivanti Service Manager (ISM)

Neste tutorial, você aprenderá integrar o ISM (Ivanti Service Manager) ao Azure AD (Azure Active Directory). Ao integrar o ISM (Ivanti Service Manager) ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao ISM (Ivanti Service Manager).
* Permitir que os usuários entrem automaticamente no ISM (Ivanti Service Manager) com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do ISM (Ivanti Service Manager) habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ISM (Ivanti Service Manager) dá suporte ao SSO iniciado por **SP e IDP**
* O ISM (Ivanti Service Manager) dá suporte ao provisionamento de usuário **Just-in-Time**

## <a name="add-ivanti-service-manager-ism-from-the-gallery"></a>Adicionar o ISM (Ivanti Service Manager) por meio da galeria

Para configurar a integração do Ivanti Service Manager (ISM) ao Azure AD, será necessário adicionar Ivanti Service Manager (ISM) da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ISM (Ivanti Service Manager)** na caixa de pesquisa.
1. Selecione **ISM (Ivanti Service Manager)** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-ivanti-service-manager-ism"></a>Configurar e testar o SSO do Azure AD para o ISM (Ivanti Service Manager)

Configure e teste o SSO do Azure AD com o ISM (Ivanti Service Manager) usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ISM (Ivanti Service Manager).

Para configurar e testar o SSO do Azure AD com o ISM (Ivanti Service Manager), execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ISM (Ivanti Service Manager)](#configure-ivanti-service-manager-ism-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do ISM (Ivanti Service Manager)](#create-ivanti-service-manager-ism-test-user)** – Para ter um equivalente de B.Fernandes no ISM (Ivanti Service Manager) vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **ISM (Ivanti Service Manager)** , localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    a. Na caixa de texto **Identificador**, digite uma URL usando um dos seguintes padrões:
    
    ```http
    https://<customer>.saasit.com/
    https://<customer>.saasiteu.com/
    https://<customer>.saasitau.com/
    ```

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<customer>.saasit.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com [equipe de suporte do cliente do Ivanti Service Manager (ISM) ](https://www.ivanti.com/support/contact) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Bruto)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

7. Na seção **Configuração do ISM (Ivanti Service Manager)** , copie as URLs apropriadas, de acordo com suas necessidades.

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

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure permitindo que ela tenha acesso ao ISM (Ivanti Service Manager).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Ivanti Service Manager (ISM)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ivanti-service-manager-ism-sso"></a>Configurar o SSO do ISM (Ivanti Service Manager)

Para configurar o logon único no **ISM (Ivanti Service Manager)** , é necessário enviar o **Certificado (bruto)** baixado e as URLs de logon copiadas do portal do Azure à [equipe de suporte do ISM (Ivanti Service Manager)](https://www.ivanti.com/support/contact). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-ivanti-service-manager-ism-test-user"></a>Criar um usuário de teste do ISM (Ivanti Service Manager)

Nesta seção, um usuário chamado Brenda Fernandes será criado no ISM (Ivanti Service Manager). O ISM (Ivanti Service Manager) dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no ISM (Ivanti Service Manager), um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte remoto do ISM (Ivanti Service Manager)](https://www.ivanti.com/support/contact).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do ISM (Ivanti Service Manager), na qual será possível iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do ISM (Ivanti Service Manager) e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao ISM (Ivanti Service Manager) para o qual o SSO foi configurado. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do ISM (Ivanti Service Manager) em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao ISM (Ivanti Service Manager) para o qual o SSO foi configurado. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o ISM (Ivanti Service Manager), você poderá impor um controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
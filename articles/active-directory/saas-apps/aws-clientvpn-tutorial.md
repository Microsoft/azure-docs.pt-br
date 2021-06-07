---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AWS ClientVPN | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AWS ClientVPN.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/29/2020
ms.author: jeedes
ms.openlocfilehash: 794934e9c3e45f2aeed9310636a0f3ec30daf7de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735324"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-clientvpn"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AWS ClientVPN

Neste tutorial, você aprenderá a integrar o AWS ClientVPN ao Azure AD (Azure Active Directory). Ao integrar o AWS ClientVPN ao Azure AD, você poderá:

* Controlar quem tem acesso ao AWS ClientVPN no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao AWS ClientVPN com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do AWS ClientVPN habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O AWS ClientVPN é compatível com SSO iniciado por **SP**

* O AWS ClientVPN dá suporte ao provisionamento de usuário **just-in-time**

## <a name="adding-aws-clientvpn-from-the-gallery"></a>Como adicionar o AWS ClientVPN por meio da galeria

Para configurar a integração do AWS ClientVPN ao Azure AD, você precisará adicionar o AWS ClientVPN por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AWS ClientVPN** na caixa de pesquisa.
1. Selecione **AWS ClientVPN** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-aws-clientvpn"></a>Configurar e testar o SSO do Azure AD para o AWS ClientVPN

Configure e teste o SSO do Azure AD com o AWS ClientVPN usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no AWS ClientVPN.

Para configurar e testar o SSO do Azure AD com o AWS ClientVPN, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do AWS ClientVPN](#configure-aws-clientvpn-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do AWS ClientVPN](#create-aws-clientvpn-test-user)** : para ter um equivalente de B.Fernandes no AWS ClientVPN que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **AWS ClientVPN**, encontre a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<LOCALHOST>`

    b. Na caixa de texto **URL de Resposta**, digite uma URL nos seguintes padrões:

    | URL de resposta |
    |------------|
    | `http://<LOCALHOST>` |
    | `https://self-service.clientvpn.amazonaws.com/api/auth/sso/saml` |
    |

    > [!NOTE]
    > Esses valores não são reais.  Atualize esses valores com a URL de resposta e a URL de logon propriamente ditas.  A URL de logon e a URL de resposta podem ter o mesmo valor (http://127.0.0.1:35001) ).  Confira a [Documentação da VPN do cliente AWS](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/client-authentication.html#ad) para obter detalhes.   Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure. Entre em contato com a [equipe de suporte do AWS ClientVPN](https://aws.amazon.com/contact-us/) para sanar qualquer problema de configuração. 

1. No serviço do Azure Active Directory, procure **Registros de aplicativo** e selecione **Todos os Aplicativos**.

1. Digite **AWS ClientVPN** na caixa de pesquisa e selecione **AWS ClientVPN** no painel de pesquisa.

1. Clique em **Manifesto**. Você precisará manter a URL de Resposta como **HTTP** em vez de **HTTPS** para que a integração funcione. Depois, clique em **Salvar**.

    ![página de manifesto](./media/aws-clientvpn-tutorial/reply-url.png)

1. O aplicativo AWS ClientVPN espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo AWS ClientVPN espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | -------------- | --------- |
    | memberOf | user.groups |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o AWS ClientVPN**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao AWS ClientVPN.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AWS ClientVPN**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-aws-clientvpn-sso"></a>Configurar o SSO do AWS ClientVPN

Para configurar o logon único no lado do **AWS ClientVPN**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do AWS ClientVPN](https://aws.amazon.com/contact-us/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-aws-clientvpn-test-user"></a>Criar um usuário de teste do AWS ClientVPN

Nesta seção, um usuário chamado Brenda Fernandes será criado no AWS ClientVPN. O AWS ClientVPN dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no AWS ClientVPN, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do AWS ClientVPN, na qual você poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do AWS ClientVPN diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do AWS ClientVPN no portal Meus Aplicativos, você será redirecionado à URL de Logon do AWS ClientVPN. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o AWS ClientVPN, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
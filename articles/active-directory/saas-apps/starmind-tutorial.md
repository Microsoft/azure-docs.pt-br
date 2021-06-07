---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Starmind | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Starmind.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/25/2020
ms.author: jeedes
ms.openlocfilehash: ecccc0bf05dfc4e27835a676748ca144c6bcc6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521978"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-starmind"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Starmind

Neste tutorial, você aprenderá a integrar o Starmind ao Azure AD (Azure Active Directory). Ao integrar o Starmind ao Azure AD, você poderá:

* Controlar quem tem acesso ao Starmind no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Starmind com suas respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Starmind.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Starmind é compatível com o SSO iniciado por **SP**
* O Starmind é compatível com o provisionamento de usuário do **Just-In-Time**

## <a name="adding-starmind-from-the-gallery"></a>Como adicionar o Starmind por meio da galeria

Para configurar a integração do Starmind ao Azure AD, será necessário adicionar o Starmind por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Starmind** na caixa de pesquisa.
1. Selecione **Starmind** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-starmind"></a>Configurar e testar o SSO do Azure AD para Starmind

Configure e teste o SSO do Azure AD com o Starmind usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, será necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Starmind.

Para configurar e testar o SSO do Azure AD com o Starmind, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Starmind](#configure-starmind-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Starmind](#create-starmind-test-user)** – ter um usuário equivalente à B.Fernandes no Starmind que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na página Integração de aplicativos do **Starmind** no portal do Azure, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.starmind.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.starmind.com/auth/realms/<ID>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.starmind.com/auth/realms/<ID>/broker/saml/endpoint`

    d. Na caixa de texto **URL de Logoff**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.starmind.com/auth/realms/<ID>/broker/saml/endpoint`


    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, o Identificador, a URL de Resposta e a URL de Logoff reais. Entre em contato com a [equipe de suporte ao Cliente do Starmind](mailto:support@starmind.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Starmind**, copie a(s) URL(s) apropriada(s) com base em seu requisito.

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

Nesta seção, você permitirá que a usuária B.Fernandes use o logon único do Azure concedendo acesso ao Starmind.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Starmind**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-starmind-sso"></a>Configurar o SSO do Starmind

Para configurar o logon único no lado do **Starmind**, será necessário enviar o **XML de Metadados Federados** baixado, além das URLs apropriadas e copiadas do portal do Azure para a [equipe de suporte do Starmind](mailto:support@starmind.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-starmind-test-user"></a>Criar um usuário de teste do Starmind

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Starmind. O Starmind é compatível com o provisionamento de usuários Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Caso um usuário ainda não exista no Starmind, após a autenticação será criado um.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Starmind, onde será possível iniciar o fluxo de logon. 

2. Acesse a URL de Logon do Starmind diretamente e inicie o fluxo de logon nela.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco do Starmind no Painel de Acesso, você será redirecionado para a URL de Logon do Starmind. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Starmind, será possível impor o controle de sessão, que protege contra infiltração e exfiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
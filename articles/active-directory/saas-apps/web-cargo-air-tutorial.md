---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Web Cargo Air | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Web Cargo Air.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: jeedes
ms.openlocfilehash: 3cca568245dfce01fb96d7a46590cb72349213ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591915"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-web-cargo-air"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Web Cargo Air

Neste tutorial, você aprenderá a integrar o Web Cargo Air ao Azure AD (Azure Active Directory). Ao integrar o Web Cargo Air ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Web Cargo Air.
* Permitir que os usuários sejam conectados automaticamente ao Web Cargo Air com suas respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Web Cargo Air.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Web Cargo Air é compatível com o SSO iniciado pelo **SP**


## <a name="adding-web-cargo-air-from-the-gallery"></a>Como adicionar o Web Cargo Air por meio da galeria

Para configurar a integração do Web Cargo Air ao Azure AD, será necessário adicionar o Web Cargo Air por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Web Cargo Air** na caixa de pesquisa.
1. Selecione **Web Cargo Air** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-web-cargo-air"></a>Configurar e testar o SSO do Azure AD para o Web Cargo Air

Configure e teste o SSO do Azure AD com o Web Cargo Air usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, será necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Web Cargo Air.

Para configurar e testar o SSO do Azure AD com o Web Cargo Air, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Web Cargo Air](#configure-web-cargo-air-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Web Cargo Air](#create-web-cargo-air-test-user)** – ter um usuário equivalente à B.Fernandes no Web Cargo Air que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na página Integração de aplicativos do **Web Cargo Air** no portal do Azure, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.webcargonet.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.webcargonet.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.webcargonet.com/saml-sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Entre em contato com a [equipe de suporte ao Cliente do Web Cargo Air](mailto:support@webcargonet.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Web Cargo Air**, copie a(s) URL(s) apropriada(s) com base em seus requisitos.

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

Nesta seção, você permitirá que a usuária B.Fernandes use o logon único do Azure concedendo acesso ao Web Cargo Air.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Web Cargo Air**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-web-cargo-air-sso"></a>Configurar o SSO do Web Cargo Air

Para configurar o logon único no lado do **Web Cargo Air**, será necessário enviar o **Certificado (Base64)** baixado, além das URLs apropriadas e copiadas do portal do Azure para a [equipe de suporte do Web Cargo Air](mailto:support@webcargonet.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-web-cargo-air-test-user"></a>Criar um usuário de teste do Web Cargo Air

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Web Cargo Air. Trabalhe com a [equipe de suporte do Web Cargo Air](mailto:support@webcargonet.com) para adicionar usuários na plataforma do Web Cargo Air. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Web Cargo Air, onde será possível iniciar o fluxo de logon. 

2. Acesse a URL de Logon do Web Cargo Air de diretamente e inicie o fluxo de logon nela.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco Web Cargo Air no Painel de Acesso, você será redirecionado para a URL de Logon do Web Cargo Air. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Web Cargo Air, será possível impor o controle de sessão, que protege contra a exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
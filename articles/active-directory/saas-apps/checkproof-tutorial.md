---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao CheckProof | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o CheckProof.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: b0afcf00a95b46472c902b24c6d4b287c1c6a482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181004"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-checkproof"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao CheckProof

Neste tutorial, você aprenderá a integrar o CheckProof ao Azure AD (Azure Active Directory). Ao integrar o CheckProof ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao CheckProof.
* Permitir que seus usuários sejam automaticamente conectados ao CheckProof com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do CheckProof.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O CheckProof dá suporte ao SSO iniciado por **IDP**

## <a name="adding-checkproof-from-the-gallery"></a>Como adicionar o CheckProof da galeria

Para configurar a integração do CheckProof ao Azure AD, você precisa adicionar o CheckProof da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **CheckProof** na caixa de pesquisa.
1. Selecione **CheckProof** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-checkproof"></a>Configurar e testar o SSO do Azure AD para o CheckProof

Configure e teste o SSO do Azure AD com o CheckProof usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do CheckProof.

Para configurar e testar o SSO do Azure AD com o CheckProof, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do CheckProof](#configure-checkproof-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste no CheckProof](#create-checkproof-test-user)** – para ter um equivalente de B.Fernandes no CheckProof que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **CheckProof**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.checkproof.com/api/v1/saml/<ID>/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.checkproof.com/api/v1/saml/<ID>/acs`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do CheckProof](mailto:support@checkproof.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o CheckProof**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao CheckProof.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **CheckProof**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-checkproof-sso"></a>Configurar o SSO do CheckProof

1. Em outra janela do navegador da Web, entre no site do CheckProof como administrador.

1. Acesse a página **Configurações > Configurações da Empresa > CONFIGURAÇÕES DO SAML** e carregue o **XML de Metadados de Federação** na caixa de texto **XML de Federação**.

    ![página de configurações do SAML](./media/checkproof-tutorial/saml-settings.png)

### <a name="create-checkproof-test-user"></a>Criar usuário de teste do CheckProof

1. Em outra janela do navegador da Web, entre no site do CheckProof como administrador.

1. Clique em **Perfil** e selecione **Meu perfil**.

    ![Página do usuário de teste do CheckProof](./media/checkproof-tutorial/create-user.png)

1. Clique em **CRIAR USUÁRIO**.

1. Na página **CRIAR USUÁRIO**, preencha os campos obrigatórios e clique em **SALVAR**.

    ![Página do usuário 1 de teste do CheckProof](./media/checkproof-tutorial/create-user-2.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

1. Clique em Testar este aplicativo no portal do Azure e você será automaticamente conectado ao CheckProof para o qual configurou o SSO

1. Use o Painel de Acesso da Microsoft. Ao clicar no bloco do CheckProof no Painel de Acesso, você deverá ser conectado automaticamente ao CheckProof para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o CheckProof, você poderá impor o Controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
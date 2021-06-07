---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Podbean | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Podbean.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/12/2020
ms.author: jeedes
ms.openlocfilehash: 1e27bd823bd4ad0428773242b5cbc0f9922925ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181757"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-podbean"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Podbean

Neste tutorial, você aprenderá a integrar o Podbean ao Azure AD (Azure Active Directory). Quando você integra o Podbean ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao Podbean.
* Permitir que os usuários sejam conectados automaticamente ao Podbean com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Podbean habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Podbean é compatível com o SSO iniciado por **SP**

* O Podbean é compatível com o provisionamento de usuário **just-in-time**

## <a name="adding-podbean-from-the-gallery"></a>Adicionar o Podbean da galeria

Para configurar a integração do Podbean com o Azure AD, será necessário adicionar o Podbean da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Podbean** na caixa de pesquisa.
1. Selecione **Podbean** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-podbean"></a>Configurar e testar o SSO do Azure AD para o Podbean

Configure e teste o SSO do Azure AD com o Podbean usando um usuário de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Podbean.

Para configurar e testar o SSO do Azure AD com o Podbean, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Podbean](#configure-podbean-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Podbean](#create-podbean-test-user)** : para ter um equivalente de B.Fernandes no Podbean vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Podbean**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://www.podbean.com/sso/<CUSTOM_ID>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao Cliente do Podbean](mailto:support@podbean.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Podbean**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Podbean.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Podbean**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-podbean-sso"></a>Configurar o SSO do Podbean

1. Em uma janela diferente do navegador da Web, entre no Podbean como Administrador.

1. Clique em **Configurações** -> **SSO de Logon** na barra lateral esquerda.

1. Clique na URL, que está mostrando a imagem abaixo, para baixar o **Arquivo de Metadados de SSO do Podbean** e salve-a no seu computador.

    ![captura de tela para baixar o Arquivo de Metadados de SSO do Podbean](./media/podbean-tutorial/sso-login.png)

1. Carregue o arquivo **XML de Metadados de Federação** no **Arquivo de Metadados de IdP de SSO**.

1. Defina o **Domínio de email** e o **Nome da Organização do SSO** e clique em **Enviar**.

    ![captura de tela para carregar o Arquivo de Metadados](./media/podbean-tutorial/metadata-file.png)

### <a name="create-podbean-test-user"></a>Criar um usuário de teste do Podbean

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Podbean. O Podbean é compatível com o provisionamento de usuário just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Podbean, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Podbean, na qual você poderá iniciar o fluxo de logon. 

2. Acesse diretamente a URL de Logon do Podbean e inicie o fluxo de logon de lá.

3. Use o Painel de Acesso da Microsoft. Quando você clicar no bloco do Podbean no Painel de Acesso, você será redirecionado à URL de Logon do Podbean. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Podbean, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Azure AD SAML Toolkit | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Kit de Ferramentas SAML do Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 26618382223a87f779f95452000a39126f37efbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675426"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Azure AD SAML Toolkit

Neste tutorial, você aprenderá a integrar o Kit de Ferramentas SAML do Azure AD ao Azure AD (Azure Active Directory). Quando você integra o Kit de Ferramentas SAML do Azure AD com o Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Kit de Ferramentas SAML do Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Kit de Ferramentas SAML do Azure AD com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Kit de Ferramentas SAML do Azure AD.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Azure AD SAML Toolkit dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Adicionar o Kit de Ferramentas SAML do Azure AD da galeria

Para configurar a integração do Kit de Ferramentas SAML do Azure AD ao Azure AD, é necessário adicionar o Kit de Ferramentas SAML do Azure AD à lista de aplicativos SaaS gerenciados da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Kit de Ferramentas SAML do Azure AD** na caixa de pesquisa.
1. Selecione **Kit de Ferramentas SAML do Azure AD** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>Configurar e testar o SSO do Azure AD para o Azure AD SAML Toolkit

Configure e teste o SSO do Azure AD com o Kit de Ferramentas SAML do Azure AD usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kit de Ferramentas SAML do Azure AD.

Para configurar e testar o SSO do Azure AD com o Azure AD SAML Toolkit, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Azure AD SAML Toolkit](#configure-azure-ad-saml-toolkit-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Azure AD SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)** – para ter um equivalente de B.Fernandes no Azure AD SAML Toolkit que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Azure AD SAML Toolkit**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://samltoolkit.azurewebsites.net/`

    b. Na caixa de texto **URL de Resposta**, digite a URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Bruto)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificateraw.png)

1. Na seção **Configurar o Kit de Ferramentas SAML do Azure AD**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que o B.Fernandes use o logon único do Azure, concedendo acesso ao Kit de Ferramentas do SAML do Azure AD.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Kit de Ferramentas do SAML do Azure AD**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Configurar o SSO do Kit de Ferramentas SAML do Azure AD

1. Abra uma nova janela do navegador da Web, se você não se registrou no site do Kit de Ferramentas do SAML do Azure AD, registre-se primeiro clicando em **Registrar**. Se você já se registrou, entre no site da empresa do Kit de Ferramentas do SAML do Azure AD usando as credenciais de login registradas.

    ![Registro do Kit de Ferramentas do SAML do Azure AD](./media/saml-toolkit-tutorial/register.png)

1. Clique na **Configuração de SAML**.

    ![Configuração de SAML do Kit de Ferramentas SAML do Azure AD](./media/saml-toolkit-tutorial/saml-configure.png)

1. Clique em **Criar**.

    ![Kit de Ferramentas do SAML do Azure AD](./media/saml-toolkit-tutorial/createsso.png)

1. Na página **Configuração do SSO do SAML**, realize as seguintes etapas:

    ![Configuração do SSO de criação do Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/fill-details.png)

    1. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    1. Na caixa de texto **Identificador do Azure AD**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    1. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiada do portal do Azure.

    1. Clique em **Escolher Arquivo** e faça upload do arquivo **Certificado (Bruto)** baixado no portal do Azure.

    1. Clique em **Criar**.

    1. Copie os valores de URL de Logon, Identificador e URL do ACS na página de configuração do SSO do Kit de Ferramentas do SAML e cole em caixas de caixa de texto respeitadas na **seção Configuração Básica de SAML** no portal do Azure.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Criar usuário de teste do Kit de Ferramentas do SAML do Azure AD

Nesta seção, um usuário chamado B.Fernandes é criado no Kit de Ferramentas do SAML do Azure AD. Crie um usuário de teste na ferramenta registrando um novo usuário e forneça todos os detalhes dele. 

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do SAML Toolkit na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de Logon do SAML Toolkit diretamente e inicie o fluxo de logon nela.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco do SAML Toolkit no Painel de Acesso, você deverá ser conectado automaticamente ao SAML Toolkit para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Azure AD SAML Toolkit, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
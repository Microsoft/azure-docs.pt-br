---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao RSA Archer Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RSA Archer Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: d96f1e0413dac8477e2fc40bf84e88318abf3f9d
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284916"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rsa-archer-suite"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao RSA Archer Suite

Neste tutorial, você aprenderá a integrar o RSA Archer Suite ao Azure AD (Azure Active Directory). Ao integrar o RSA Archer Suite ao Azure AD, você poderá:

* Controlar quem tem acesso ao RSA Archer Suite no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao RSA Archer Suite com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do RSA Archer Suite habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O RSA Archer Suite dá suporte ao SSO iniciado por **SP**.
* O RSA Archer Suite dá suporte ao provisionamento de usuário **just-in-time**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-rsa-archer-suite-from-the-gallery"></a>Adicionar o RSA Archer Suite por meio da galeria

Para configurar a integração do RSA Archer Suite ao Azure AD, é necessário adicioná-lo por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **RSA Archer Suite** na caixa de pesquisa.
1. Selecione **RSA Archer Suite** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-rsa-archer-suite"></a>Configurar e testar o SSO do Azure AD para o RSA Archer Suite

Configure e teste o SSO do Azure AD com o RSA Archer Suite usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do RSA Archer Suite.

Para configurar e testar o SSO do Azure AD com o RSA Archer Suite, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do RSA Archer Suite](#configure-rsa-archer-suite-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do RSA Archer Suite](#create-rsa-archer-suite-test-user)** : para ter um equivalente de B.Fernandes no RSA Archer Suite que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **RSA Archer Suite**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<BASE_URL>/default.aspx?IDP=<REALM_NAME>`

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite o valor: `RSAArcherSuite_TENANT_STRING`

    > [!NOTE]
    > O valor da URL de Entrada não é real. Atualize o valor com a URL de Entrada real. Entre em contato com a [equipe de suporte ao cliente do RSA Archer Suite](mailto:archersupport@rsa.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo RSA Archer Suite espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo RSA Archer Suite espera que mais alguns atributos sejam transmitidos novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | -------------- | --------- |
    | Nome | user.givenname |
    | LastName | user.surname |
    | PhoneNumber | user.telephonenumber |
    | City | user.city |
    | CEP | user.postalcode |
    | Estado | user.state |
    | Street | user.streetaddress |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o RSA Archer Suite**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao RSA Archer Suite.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **RSA Archer Suite**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-rsa-archer-suite-sso"></a>Configurar o SSO do RSA Archer Suite

1. Entre no site do RSA Archer Suite em outro navegador como administrador.

1. Execute as etapas abaixo na página a seguir.

    ![Configurar o SSO do RSA Archer Suite](./media/rsa-archer-suite-tutorial/configuration.png)

    a. Acesse a guia **Logon Único** e selecione **SAML** como um **Modo de Logon Único** na lista suspensa.

    b. Marque a caixa de seleção **Permitir bypass manual**.

    c. Forneça um nome válido na caixa de texto **ID da Entidade de Instância**.

    d. Cole o **Valor da Impressão Digital** na caixa de texto **Impressão Digital do Certificado**.

    e. Clique no botão **Selecionar** e carregue o arquivo **XML de Metadados de Federação** baixado do portal do Azure.

    f. **Salve** as configurações de logon único. 

### <a name="create-rsa-archer-suite-test-user"></a>Criar um usuário de teste do RSA Archer Suite

Nesta seção, um usuário chamado B.Fernandes será criado no RSA Archer Suite. O RSA Archer Suite dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no RSA Archer Suite, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do RSA Archer Suite, na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de Logon do RSA Archer Suite diretamente e inicie o fluxo de logon nele.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco do RSA Archer Suite no Painel de Acesso, você será conectado automaticamente ao RSA Archer Suite, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

 Depois de configurar o RSA Archer Suite, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
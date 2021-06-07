---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PureCloud by Genesys | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PureCloud by Genesys.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 7c6f84ee3bb4920dbe57221b8b0bbf9f5880742b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652980"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PureCloud by Genesys

Neste tutorial, você aprenderá a integrar o PureCloud by Genesys ao Azure AD (Azure Active Directory). Ao integrar o PureCloud by Genesys ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao PureCloud by Genesys.
* Permitir que os usuários sejam conectados automaticamente ao PureCloud by Genesys com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do PureCloud by Genesys.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O PureCloud by Genesys dá suporte ao SSO iniciado por **SP e IDP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-purecloud-by-genesys-from-the-gallery"></a>Adicionar o PureCloud by Genesys por meio da galeria

Para configurar a integração do PureCloud by Genesys ao Azure AD, você precisará adicionar o PureCloud by Genesys por meio da galeria à lista de aplicativos SaaS gerenciados. Para fazer isso, siga estas etapas:

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Acesse **Aplicativos Empresariais** e, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **PureCloud by Genesys** na caixa de pesquisa.
1. Selecione **PureCloud by Genesys** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-purecloud-by-genesys"></a>Configurar e testar o SSO do Azure AD para o PureCloud by Genesys

Configure e teste o SSO do Azure AD com o PureCloud by Genesys usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PureCloud by Genesys.

Para configurar e testar o SSO do Azure AD com o PureCloud by Genesys, execute as seguintes etapas:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do PureCloud by Genesys](#configure-purecloud-by-genesys-sso)** para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** : para ter um equivalente de B.Fernandes no PureCloud by Genesys que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure, siga estas etapas:

1. No portal do Azure, na página de integração do aplicativo **PureCloud by Genesys**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, insira os valores para os seguintes campos:

    a. Na caixa **Identificador**, insira as URLs que correspondem à sua região:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. Na caixa **URL de Resposta**, insira as URLs que correspondem à sua região:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Selecione **Definir URLs adicionais** e execute as seguintes etapas caso deseje configurar o aplicativo no modo iniciado por **SP**:

    Na caixa **URL de Logon**, insira as URLs que correspondem à sua região:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. O aplicativo PureCloud by Genesys espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão:

    ![image](common/default-attributes.png)

1. Além disso, o aplicativo PureCloud by Genesys espera que mais alguns atributos sejam passados de volta na resposta SAML, conforme mostrado na tabela a seguir. Esses atributos também são pré-populados, mas você pode examiná-los conforme necessário.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o PureCloud by Genesys**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B.Fernandes:

1. No painel esquerdo no portal do Azure, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira o nome de usuário no seguinte formato: username@companydomain.extension. Por exemplo: `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao PureCloud by Genesys.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **PureCloud by Genesys**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-purecloud-by-genesys-sso"></a>Configurar o SSO do PureCloud by Genesys

1. Em outra janela do navegador da Web, entre no PureCloud by Genesys como administrador.

1. Selecione **Administrador** na parte superior e, em seguida, vá para **Logon Único** em **Integrações**.

    ![Captura de tela que mostra a janela Administrador do PureCloud, na qual você pode selecionar Logon Único.](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. Alterne para a guia **ADFS/Azure AD (Premium)** e siga estas etapas:

    ![Captura de tela que mostra a página Integrações, na qual você pode inserir os valores descritos.](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. Selecione **Procurar** para carregar o certificado codificado em base-64 baixado no portal do Azure no **Certificado do ADFS**.

    b. Na caixa **URI do Emissor do ADFS**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa **URI de Destino**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Para o valor do **Identificador de Terceira Parte Confiável**, acesse o portal do Azure e, na página de integração de aplicativos do **PureCloud by Genesys**, selecionar a guia **Propriedades** e copiar o valor da **ID do Aplicativo**. Cole-o na caixa **Identificador de Terceira Parte Confiável**.

    ![Captura de tela que mostra o painel Propriedades, no qual você pode encontrar o valor ID do Aplicativo.](./media/purecloud-by-genesys-tutorial/configure-6.png)

    e. Clique em **Salvar**.

### <a name="create-purecloud-by-genesys-test-user"></a>Criar um usuário de teste do PureCloud by Genesys

Para permitir que os usuários do Azure AD entrem no PureCloud by Genesys, eles precisam ser provisionados no PureCloud by Genesys. No PureCloud by Genesys, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, siga estas etapas:**

1. Faça logon no PureCloud by Genesys como administrador.

1. Selecione **Administrador** na parte superior e vá para **Pessoas** em **Pessoas e Permissões**.

    ![Captura de tela que mostra a janela Administrador do PureCloud, na qual você pode selecionar Pessoas.](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. Na página **Pessoas**, selecione **Adicionar Pessoa**.

    ![Captura de tela que mostra a página Pessoas, na qual você pode adicionar uma pessoa.](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. Na caixa de diálogo **Adicionar Pessoas à Organização**, siga estas etapas:

    ![A captura de tela mostra a página em que você pode inserir os valores descritos.](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. Na caixa **Nome Completo**, digite o nome do usuário. Por exemplo:  **B.fernandes**.

    b. Na caixa **Email**, insira o email do usuário. Por exemplo: **b.fernandes\@contoso.com**.

    c. Selecione **Criar**.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Entrada do PureCloud by Genesys, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do PureCloud by Genesys e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no PureCloud by Genesys, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do PureCloud by Genesys em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no PureCloud by Genesys, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o PureCloud by Genesys, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
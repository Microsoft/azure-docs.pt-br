---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Netskope Administrator Console | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Netskope Administrator Console.
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
ms.openlocfilehash: 5f07e9dbd574d8a27e6048154c72b9afc24b6ba4
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286077"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Netskope Administrator Console

Neste tutorial, você aprenderá a integrar o Netskope Administrator Console ao Azure AD (Azure Active Directory). Ao integrar o Netskope Administrator Console ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Netskope Administrator Console.
* Permitir que os usuários sejam conectados automaticamente ao Netskope Administrator Console com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Netskope Administrator Console.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Netskope Administrator Console dá suporte ao SSO iniciado por **SP e IdP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Adicionar o Netskope Administrator Console por meio da galeria

Para configurar a integração do Netskope Administrator Console ao Azure AD, você precisa adicionar o Netskope Administrator Console da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Netskope Administrator Console** na caixa de pesquisa.
1. Selecione **Netskope Administrator Console** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Configurar e testar o SSO do Azure AD para o Netskope Administrator Console

Configure e teste o SSO do Azure AD com o Netskope Administrator Console usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre o usuário do Azure AD e o usuário relacionado no Netskope Administrator Console.

Para configurar e testar o SSO do Azure AD com o Netskope Administrator Console, realize as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Netskope Administrator Console](#configure-netskope-administrator-console-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Netskope Administrator Console](#create-netskope-administrator-console-test-user)** – para ter um equivalente de Brenda Fernandes no Netskope Administrator Console que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Netskope Administrator Console**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Resposta real. Você obterá o valor explicado mais adiante no tutorial.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Os valores da URL de Logon não é real. Atualize o valor da URL de Logon com a URL de Logon real. Entre em contato com [equipe de suporte do cliente do Netskope Administrator Console](mailto:support@netskope.com) para obter o valor da URL de logon. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Netskope Administrator Console espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Netskope Administrator Console espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de Origem|
    | ---------| --------- |
    | admin-role | user.assignedroles |

    > [!NOTE]
    > Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como criar funções no Azure AD.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Netskope Administrator Console**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Netskope Administrator Console.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Netskope Administrator Console**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você tiver configurado as funções conforme explicado acima, será possível selecioná-las na lista suspensa **Selecionar uma função**.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-netskope-administrator-console-sso"></a>Configurar o SSO do Netskope Administrator Console

1. Abra uma nova guia em seu navegador e entre no site da empresa do Console do Administrador Netskope como administrador.

1. Clique na guia **Configurações** do painel de navegação esquerdo.

    ![A captura de tela mostra a opção Configuração selecionada no painel de navegação.](./media/netskope-cloud-security-tutorial/configure-settings.png)

1. Clique na guia **Administração**.

    ![A captura de tela mostra a opção Administração selecionada em Configurações.](./media/netskope-cloud-security-tutorial/administration.png)

1. Clique na guia **SSO**.

    ![A captura de tela mostra a opção SSO selecionada em Administração.](./media/netskope-cloud-security-tutorial/tab.png)

1. Na seção **Configurações de Rede**, execute as seguintes etapas:
    
    ![A captura de tela mostra a opção Configurações de Rede em que você pode inserir os valores descritos.](./media/netskope-cloud-security-tutorial/network.png)

    a. Copie o valor da **URL do Serviço do Consumidor de Declaração** e cole-o na caixa de texto **URL de Resposta** da seção **Configuração Básica de SAML** do portal do Azure.

    b. Copie o valor da **ID da Entidade do Provedor de Serviço** e cole-o na caixa **Identificador** da seção **Configuração Básica de SAML** do portal do Azure.

1. Clique em **EDITAR CONFIGURAÇÕES** na seção **Configurações de SSO/SLO**.

    ![A captura de tela mostra as Configurações de SSO/SLO em que você pode selecionar EDITAR CONFIGURAÇÕES.](./media/netskope-cloud-security-tutorial/settings.png)

1. Na janela pop-up **Configurações**, execute as seguintes etapas;

    ![A captura de tela mostra a caixa de diálogo Configurações em que você pode inserir os valores descritos.](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Selecione **Habilitar SSO**.

    b. Na caixa de texto **URL de IDP**, cole o valor **URL de Logon**, que você copiou do portal do Azure.

    c. Na caixa de texto **ID DA ENTIDADE DO IDP**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    d. Abra seu certificado codificado com Base64 baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **CERTIFICADO DO IDP**.

    e. Selecione **Habilitar SSO**.

    f. Na caixa de texto **URL de SLO do IDP**, cole o valor da **URL de Logout** copiado do portal do Azure.

    g. Clique em **ENVIAR**.

### <a name="create-netskope-administrator-console-test-user"></a>Criar usuário de teste do Netskope Administrator Console

1. Abra uma nova guia em seu navegador e entre no site da empresa do Console do Administrador Netskope como administrador.

1. Clique na guia **Configurações** do painel de navegação esquerdo.

    ![A captura de tela mostra a opção Configurações selecionada.](./media/netskope-cloud-security-tutorial/configure-settings.png)

1. Clique em guia **Plataforma Ativa**.

    ![A captura de tela mostra a Plataforma Ativa selecionada em Configurações.](./media/netskope-cloud-security-tutorial/user-1.png)

1. Clique na guia **Usuários**.

    ![A captura de tela mostra a opção Usuários selecionada na Plataforma Ativa.](./media/netskope-cloud-security-tutorial/add-user.png)

1. Clique em **ADICIONAR USUÁRIOS**.

    ![A captura de tela mostra a caixa de diálogo Usuários em que você pode selecionar ADICIONAR USUÁRIOS.](./media/netskope-cloud-security-tutorial/user-add.png)

1. Insira o endereço de email do usuário que você deseja adicionar e clique em **ADICIONAR**.

    ![A captura de tela mostra Adicionar Usuários em que você pode inserir uma lista de usuários.](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Netskope Administrator Console, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Netskope Administrator Console e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Netskope Administrator Console para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Netskope Administrator Console em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Netskope Administrator Console para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Netskope Administrator Console, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

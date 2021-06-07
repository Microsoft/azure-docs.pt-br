---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Slack | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Slack.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 6e2428967b8e3b4c677752955ea743c5b7d144e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729579"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Slack

Neste tutorial, você aprenderá a integrar o Slack com o Azure AD (Azure Active Directory). Ao integrar o Slack no Azure AD, você pode:

* controlar no Azure AD quem tem acesso ao Slack.
* Permitir que os usuários sejam conectados automaticamente ao Slack com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Slack.

> [!NOTE]
> Se você precisar integrar com mais de uma instância do Slack em um locatário, o identificador de cada aplicativo poderá ser uma variável.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Slack dá suporte ao SSO iniciado por **SP**
* O Slack é compatível com o provisionamento de usuário **Just In Time**
* O Slack dá suporte ao [provisionamento **automatizado** de usuários](./slack-provisioning-tutorial.md)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-slack-from-the-gallery"></a>Adicionar o Slack da galeria

Para configurar a integração do Slack ao Azure AD, você precisa adicionar o Slack por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Slack** na caixa de pesquisa.
1. Selecione **Slack** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Configurar e testar o SSO do Azure AD para o Slack

Configure e teste o SSO do Azure AD com o Slack usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Slack.

Para configurar e testar o SSO do Azure AD com o Slack, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Slack](#configure-slack-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Slack](#create-slack-test-user)** – para ter um equivalente de B.Fernandes no Slack que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Slack**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://slack.com`
    
    c. Para a **URL de Resposta**, insira um dos seguintes padrões de URLs:
    
    | URL de resposta|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Entrada e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do Slack](https://slack.com/help/contact) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

    > [!NOTE]
    > O valor para **Identificador (ID da Entidade)** poderá ser uma variável se você tiver mais de uma instância do Slack que precise ser integrada ao locatário. Use o padrão `https://<DOMAIN NAME>.slack.com`. Nesse cenário, você também precisa emparelhar com outra configuração no Slack usando o mesmo valor.

1. O aplicativo Slack espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo Slack espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos. Você também precisa adicionar o atributo `email`. Se o usuário não tiver um endereço de email, mapeie **emailaddress** para **user.userprincipalname** e mapeie **email** para **user.userprincipalname**.

    | Nome | Atributo de Origem |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | email | user.userprincipalname |

   > [!NOTE]
   > Para definir a configuração do SP (provedor de serviços), você deve clicar em **Expandir** ao lado de **Opções Avançadas** na página de configuração de SAML. Na caixa **Emissor do Provedor de Serviços**, insira a URL do workspace. O padrão é slack.com. 

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Slack**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Slack.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Slack**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-slack-sso"></a>Configurar o SSO do Slack

1. Para automatizar a configuração no Slack, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, clique em **Configurar o Slack** e você será direcionado ao aplicativo Slack. Nele, forneça as credenciais de administrador para entrar no Slack. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Slack manualmente, em uma janela diferente do navegador da Web, entre no site da empresa do Slack como administrador.

2. Navegue até **Microsoft Azure AD** e depois em **Configurações de Equipe**.

     ![Configurar o logon único no Microsoft Azure AD](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. Na seção **Configurações de Equipe**, clique na guia **Autenticação** e em **Alterar Configurações**.

    ![Configurar o logon único nas Configurações da Equipe](./media/slack-tutorial/tutorial-slack-authentication.png)

4. No diálogo **Configurações de Autenticação SAML** , realize as seguintes etapas:

    ![Configurar o logon único das Configurações de Autenticação do SAML](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  Na caixa de texto **Ponto de Extremidade do SAML 2.0 (HTTP)** , cole o valor da **URL de Logon** copiado do portal do Azure.

    b.  Na caixa de texto **Emissor de provedor de identidade**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.

    c.  Abra seu arquivo de certificado baixado no Bloco de Notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado Público**.

    d. Defina as três configurações acima conforme apropriado para sua equipe do Slack. Para saber mais sobre as configurações, encontre o **guia de configuração de SSO do Slack** aqui. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Clique em **expandir** e insira `https://slack.com` na caixa de texto **Emissor do provedor de identidade**.

    f.  Clique em **Salvar Configuração**.
    
    > [!NOTE]
    > Se você tiver mais de uma instância do Slack que precise ser integrada ao Azure AD, defina `https://<DOMAIN NAME>.slack.com` para **Emissor do provedor de serviços** para que ele possa ser emparelhado com a configuração **Identificador** do aplicativo do Azure.

### <a name="create-slack-test-user"></a>Criar um usuário de teste do Slack

O objetivo desta seção é criar um usuário chamado B.Fernandes no Slack. O Slack dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Slack, caso ele ainda não exista. O Slack também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](slack-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático do usuário.

> [!NOTE]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Slack](https://slack.com/help/contact).

> [!NOTE]
> O Azure AD Connect é a ferramenta de sincronização que pode sincronizar Identidades do Active Directory local ao Azure AD e, em seguida, esses usuários sincronizados também poderão usar os aplicativos como outros usuários de nuvem.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em **Testar este aplicativo** no portal do Azure. Isso o redirecionará para a URL de Logon do Slack, na qual você poderá iniciar o fluxo de logon.

* Acesse a URL de Logon do Slack diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Slack em Meus Aplicativos, isso redirecionará você à URL de Logon do Slack. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Slack, você poderá impor um controle de sessão, que fornece proteção contra o vazamento e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
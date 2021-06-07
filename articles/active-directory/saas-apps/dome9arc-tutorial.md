---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Check Point CloudGuard Dome9 Arc | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Check Point CloudGuard Dome9 Arc.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e5bb8a32cfd73e67141a25531594e8a3b6f793c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732173"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Check Point CloudGuard Dome9 Arc

Neste tutorial, você aprenderá a integrar o Check Point CloudGuard Dome9 Arc ao Azure Active Directory (Azure AD). Ao integrar o Check Point CloudGuard Dome9 Arc ao Azure AD, você pode:

* Controlar no Azure AD quem terá acesso ao Check Point CloudGuard Dome9 Arc.
* Permitir que os usuários sejam conectados automaticamente ao Check Point CloudGuard Dome9 Arc com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Check Point CloudGuard Dome9 Arc.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Check Point CloudGuard Dome9 Arc é compatível com SSO iniciado por **SP e IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Como adicionar o Check Point CloudGuard Dome9 Arc da galeria

Para configurar a integração do Check Point CloudGuard Dome9 Arc ao Azure AD, será necessário adicionar o Check Point CloudGuard Dome9 Arc da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Check Point CloudGuard Dome9 Arc** na caixa de pesquisa.
1. Selecione **Check Point CloudGuard Dome9 Arc** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-check-point-cloudguard-dome9-arc"></a>Configurar e testar o SSO do Azure AD para o Check Point CloudGuard Dome9 Arc

Configure e teste o SSO do Azure AD com o Check Point CloudGuard Dome9 Arc usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Check Point CloudGuard Dome9 Arc.

Para configurar e testar o SSO do Azure AD com o Check Point CloudGuard Dome9 Arc, realize as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Check Point CloudGuard Dome9 Arc](#configure-check-point-cloudguard-dome9-arc-sso)** para definir as configurações de logon único no lado do aplicativo.
    1. **[Crie um usuário de teste do Check Point CloudGuard Dome9 Arc](#create-check-point-cloudguard-dome9-arc-test-user)** – para ter um equivalente de B.Fernandes no Check Point CloudGuard Dome9 Arc vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Check Point CloudGuard Dome9 Arc**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Você obterá o valor `<company name>` na seção **Configurar o SSO do Check Point CloudGuard Dome9 Arc**, que será explicado mais adiante no tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Check Point CloudGuard Dome9 Arc espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo Check Point CloudGuard Dome9 Arc espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.
    
    | Nome |  Atributo de Origem|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como criar funções no Azure AD.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Check Point CloudGuard Dome9 Arc**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Check Point CloudGuard Dome9 Arc.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Check Point CloudGuard Dome9 Arc**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você tiver configurado as funções conforme explicado acima, será possível selecioná-las na lista suspensa **Selecionar uma função**.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Configurar o SSO do Check Point CloudGuard Dome9 Arc

1. Para automatizar a configuração no Check Point CloudGuard Dome9 Arc, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clicar em **Configurar Check Point CloudGuard Dome9 Arc** o direcionará ao aplicativo Check Point CloudGuard Dome9 Arc. Forneça aí as credenciais de administrador para entrar no Check Point CloudGuard Dome9 Arc. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Caso deseje configurar o Check Point CloudGuard Dome9 Arc manualmente, abra uma nova janela do navegador da Web, entre no site de empresa do Check Point CloudGuard Dome9 Arc como administrador e execute as seguintes etapas:

2. Clique nas **Configurações de Perfil** no canto superior direito e depois em **Configurações de Conta**. 

    ![Captura de tela que mostra o menu "Configurações de Perfil" com "Configurações da Conta" selecionado.](./media/dome9arc-tutorial/configure1.png)

3. Navegue até **SSO** e, em seguida, clique em **HABILITAR**.

    ![Captura de tela que mostra a guia "SSO" e "Habilitar" selecionados.](./media/dome9arc-tutorial/configure2.png)

4. Na seção de Configuração de SSO, execute as seguintes etapas:

    ![Configuração do Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

    a. Digite o nome da empresa na caixa de texto **ID da Conta**. Esse valor deve ser usado na URL de **Resposta** e **Entrada** mencionada na seção **Configuração Básica do SAML** do portal do Azure.

    b. Na caixa de texto **Emissor**, cole o valor de **Identificador do Azure AD** que você copiou do portal do Azure.

    c. Na caixa de texto **URL do ponto de extremidade IDP**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    d. Abra seu certificado codificado com Base64 baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado X.509**.

    e. Clique em **Save** (Salvar).

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Criar o usuário de teste do Check Point CloudGuard Dome9 Arc

Para permitir que os usuários do Azure AD entrem no Check Point CloudGuard Dome9 Arc, eles devem ser provisionados no aplicativo. O Check Point CloudGuard Dome9 Arc é compatível com provisionamento just-in-time, mas, para que ele funcione corretamente, o usuário deve selecionar uma **Função** específica e atribuir o mesmo ao usuário.

   >[!Note]
   >Para saber sobre a criação da **Função** e outros detalhes, contate a [equipe de suporte ao cliente do Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com).

**Para provisionar uma conta de usuário manualmente, execute as seguintes etapas:**

1. Entre no site da empresa do seu Check Point CloudGuard Dome9 Arc como administrador.

2. Clique em **Usuários e Funções** e, em seguida, clique em **Usuários**.

    ![Captura de tela que mostra "Usuários e Funções" com a ação "Usuários" selecionada.](./media/dome9arc-tutorial/user1.png)

3. Clique em **ADICIONAR USUÁRIO**.

    ![Captura de tela que mostra "Usuários e Funções" com o botão "ADICIONAR USUÁRIO" selecionado.](./media/dome9arc-tutorial/user2.png)

4. Na seção **Criar Usuário** , realize as seguintes etapas:

    ![Adicionar Funcionário](./media/dome9arc-tutorial/user3.png)

    a. Na caixa de texto **Email**, digite o email do usuário como B.Simon@contoso.com.

    b. Na caixa de texto **Nome**, digite o nome do usuário, como Brenda.

    c. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como Fernandes.

    d. Faça o **Usuário do SSO** como **On**.

    e. Clique em **CRIAR**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado para a URL de Entrada do Check Point CloudGuard Dome9 Arc, local em que poderá iniciar o fluxo de logon.  

* Acesse a URL de Entrada do Check Point CloudGuard Dome9 Arc diretamente e inicie o fluxo de logon nesse local.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será automaticamente conectado ao Check Point CloudGuard Dome9 Arc para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Check Point CloudGuard Dome9 Arc em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Check Point CloudGuard Dome9 Arc para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Check Point CloudGuard Dome9 Arc, você poderá impor o Controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

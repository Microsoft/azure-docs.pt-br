---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Workday | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181362"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Workday

Neste tutorial, você aprenderá a integrar o Workday ao Azure AD (Azure Active Directory). Quando integrar o Workday ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Workday.
* Permitir que os usuários sejam conectados automaticamente ao Workday com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Workday.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Workday é compatível com o SSO iniciado por **SP**.

* Agora, o aplicativo móvel do Workday pode ser configurado com o Azure AD para habilitar o SSO. Para obter mais detalhes sobre como configurar, siga [este](workday-mobile-tutorial.md) link.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-workday-from-the-gallery"></a>Adicionar o Workday da galeria

Para configurar a integração do Workday com o Azure AD, é necessário adicionar o Workday da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Workday** na caixa de pesquisa.
1. Escolha **Workday** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Configurar e testar o SSO do Azure AD para Workday

Configure e teste o SSO do Azure AD com o Workday usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Workday.

Para configurar e testar o SSO do Azure AD com o Workday, execute as seguintes etapas:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
2. **[Configure o Workday](#configure-workday)** para definir as configurações de SSO no lado do aplicativo.
    1. **[Criar um usuário de teste do Workday](#create-workday-test-user)** para ter um equivalente de B.Fernandes no Workday que esteja vinculado à representação de usuário do Azure AD.
3. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Workday**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml.htmld`

    c. Na caixa de texto **URL de Logoff**, digite uma URL usando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Esses não são os valores reais. Atualize esses valores com a URL de Logon, a URL de Resposta e a URL de Logoff reais. Sua URL de resposta deve ter um subdomínio, por exemplo: www, wd2, wd3, wd3-impl, wd5, wd5-impl.
    > Usar algo como `http://www.myworkday.com` funciona, mas `http://myworkday.com` não. Contate a [equipe de suporte ao cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Seu aplicativo Workday espera as instruções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo Workday espera que **nameidentifier** seja mapeado com **user.mail**, **UPN**, etc. Portanto, você precisa editar o mapeamento de atributos clicando no ícone **Editar** e alterar o mapeamento de atributos.

    ![Captura de tela que mostra Atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

    > [!NOTE]
    > Aqui nós Mapeamos a identificação do nome UPN (userPrincipalName) como padrão. Você precisa mapear a ID de nome com a ID de usuário real em sua conta do Workday (seu email, UPN, etc.) para que o SSO funcione corretamente.

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Para modificar as opções de **Autenticação** de acordo com seus requisitos, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Certificado](common/edit-certificate.png) 

    ![Certificado de assinatura de SAML](./media/workday-tutorial/signing-option.png)

    a. Escolha **Assinar resposta SAML e declaração** na **Opção de Assinatura**.

    b. Clique em **Salvar**

1. Na seção **Configurar o Workday**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Workday.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Workday**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-workday"></a>Configurar o Workday

1. Em uma janela diferente do navegador da Web, faça logon no site da empresa Workday como administrador.

1. Na **Caixa de pesquisa**, procure com o nome **Editar Configuração de Locatário – Segurança** no lado superior esquerdo da home page.

    ![Editar Segurança de Locatário](./media/workday-tutorial/IC782925.png "Editar Segurança de Locatário")


1. Na seção **Configuração de SAML**, execute as etapas a seguir:

    ![Configuração do SAML](./media/workday-tutorial/IC782926.png "Configuração do SAML")

    a.  Selecione **Habilitar Autenticação SAML**.

    b.  Clique em **Adicionar Linha**.

1. Na seção **Provedores de Identidade SAML**, execute as ações a seguir para a linha recém-criada.

    a. Execute as ações a seguir para os campos, que são mostrados abaixo.

    ![Provedores de Identidade SAML 1](./media/workday-tutorial/IC7829271.png "Provedores de Identidade do SAML")

    * Na caixa de texto **Nome do Provedor de Identidade**, digite um nome de provedor (por exemplo: *SPInitiatedSSO*).

    * No Portal do Azure, na janela **Configurar Workday**, o valor do **Identificador do Azure AD** e, em seguida, cole-o na caixa de texto **Emissor**.

    * Abra o **Certificado** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado x.509**.

    b. Execute as ações a seguir para os campos, que são mostrados abaixo.

    ![Provedores de Identidade SAML 2](./media/workday-tutorial/saml-identity-provider-2.png "Provedores de Identidade do SAML")

    * Marque a caixa de seleção **Habilitar Logoff Iniciado pelo IdP**.

    * Na caixa de texto **URL de Resposta de Logoff**, digite **http://www.workday.com** .

    * Na caixa de texto **URL de Solicitação de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    * Marque a caixa de seleção **Iniciado por SP**.

    * Na caixa de texto **ID do Provedor de Serviço**, digite **http://www.workday.com**.


    * Selecione **Não desinflar a solicitação de autenticação iniciada pelo SP**.

    c. Execute as ações a seguir para os campos, que são mostrados abaixo.

    ![Provedores de Identidade SAML 3](./media/workday-tutorial/saml-identity-provider-3.png "Provedores de Identidade do SAML")

    * No Portal do Azure, na janela **Configurar Workday**, copie a **URL de Logon** e, em seguida, cole-a na caixa de texto **URL do Serviço de SSO do IdP**.

    * Na caixa de texto **Usado para Ambientes**, selecione os nomes do ambiente apropriados na lista suspensa.

1. Execute as etapas a seguir na imagem abaixo.

    ![Workday](./media/workday-tutorial/service-provider.png "Provedores de Identidade do SAML")

    a. Na caixa de texto **ID do Provedor de Serviço (Será Preterido)** , digite **http://www.workday.com** .

    b. Na caixa de texto **URL do Serviço de SSO do IdP (será preterido)** , digite o valor da **URL de Logon**.

    c. Selecione **Não Esvazie a Solicitação de Autenticação iniciada por SP (será preterida)** .

    d. Para **Método de Assinatura de Solicitação de Autenticação**, selecione **SHA256**.

    e. Clique em **OK**.

    > [!NOTE]
    > Verifique se que você configurar o logon único corretamente. Caso você habilite o logon único com a configuração incorreta, talvez não seja possível inserir o aplicativo com suas credenciais e ser bloqueado. Nessa situação, o Workday fornece uma URL de login de backup em que os usuários podem entrar usando seu nome de usuário e senha normais no seguinte formato: [Your Workday URL] /login.flex?redirect=n

### <a name="create-workday-test-user"></a>Criar um usuário de teste do Workday

1. Entre no site da empresa do Workday como administrador.

1. Clique em **Perfil** no canto superior direito, selecione **Página Inicial** e clique em **Diretório** na guia **Aplicativos**. 

1. Na página **Diretório**, selecione **Localizar Trabalhadores** na guia de exibição.

    ![Localizar trabalhadores](./media/workday-tutorial/user-directory.png)

1.  Na página **Localizar Trabalhos**, selecione o usuário nos resultados.

1. Na página a seguir, selecione **Trabalho > Segurança do Trabalhador** e a **Conta do Workday** deve corresponder ao Azure Active Directory como o valor do **ID do Nome**.

    ![Segurança do Trabalhador](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Para obter mais informações sobre como criar um usuário de teste do Workday, entre em contato com [equipe de suporte ao cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Workday, na qual poderá iniciar o fluxo de logon. 

2. Acesse a URL de Logon do Workday diretamente e inicie o fluxo de logon de lá.

3. Use o Painel de Acesso da Microsoft. Ao clicar no bloco do Workday no Painel de Acesso, você deverá ser conectado automaticamente ao Workday, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Workday, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
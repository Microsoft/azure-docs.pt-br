---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Workplace by Facebook| Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workplace by Facebook.
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
ms.openlocfilehash: 3f66da38d3303b47c2a9b6cefeee19af6bf64ec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725483"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Workplace by Facebook

Neste tutorial, você aprenderá a integrar o Workplace by Facebook ao Azure AD (Azure Active Directory). Ao integrar o Workplace by Facebook ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Workplace by Facebook.
* Permitir que seus usuários entrem automaticamente no Workplace by Facebook com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Workplace by Facebook habilitada para o SSO (logon único).

> [!NOTE]
> O Facebook tem dois produtos, o Workplace Standard (gratuito) e o Workplace Premium (pago). Qualquer locatário do Workplace Premium pode configurar a integração de SCIM e SSO sem outras implicações para o custo ou as licenças necessárias. O SSO e o SCIM não estão disponíveis nas instâncias do Workplace Standard.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Workplace by Facebook dá suporte a SSO iniciado por **SP**
* O Workplace by Facebook dá suporte a **provisionamento Just-In-Time**
* O Workplace by Facebook dá suporte a **[provisionamento de usuário automático](workplacebyfacebook-provisioning-tutorial.md)**
* Agora, o aplicativo móvel do Workplace by Facebook pode ser configurado com o Azure AD para habilitar o SSO. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.


## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Adicionar o Workplace by Facebook da Galeria

Para configurar a integração do Workplace by Facebook ao Azure AD, você precisa adicionar o Workplace by Facebook da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Workplace by Facebook** na caixa de pesquisa.
1. Selecione **Workplace by Facebook** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Configurar e testar o SSO do Azure AD para Workplace by Facebook

Configure e teste o SSO do Azure AD com o Workplace by Facebook usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Workplace by Facebook.

Para configurar e testar o SSO do Azure AD com o Workplace by Facebook, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Workplace by Facebook](#configure-workplace-by-facebook-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    1. **[Criar usuário de teste no Workplace by Facebook](#create-workplace-by-facebook-test-user)** – para ter um equivalente de B.Fernandes no Workplace by Facebook que esteja vinculado à representação deste usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Workplace by Facebook**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** (encontrada no WorkPlace como a URL do destinatário), digite uma URL usando o seguinte padrão: `https://.workplace.com/work/saml.php`

    b. Na caixa de texto **Identificador (ID da Entidade)** (encontrada no WorkPlace como a URL do público-alvo), digite uma URL usando o seguinte padrão: `https://www.workplace.com/company/`

    c. Na caixa de texto **URL de Resposta** (encontrada no WorkPlace como o Serviço do Consumidor de Asserção), digite uma URL usando o seguinte padrão: `https://.workplace.com/work/saml.php`

    > [!NOTE]
    > Esses não são os valores reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Confira a página Autenticação do Workplace Company Dashboard a fim de obter os valores corretos para a comunidade do Workplace. Isso será explicado posteriormente no tutorial.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Workplace by Facebook**, copie a(s) URL(s) apropriada(s) com base em seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso ao Workplace by Facebook.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Workplace by Facebook**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-workplace-by-facebook-sso"></a>Configurar o SSO do Workplace by Facebook

1. Para automatizar a configuração no Workplace by Facebook, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após adicionar a extensão ao navegador, um clique em **Configurar o Workplace by Facebook** direcionará você ao aplicativo Workplace by Facebook. Nele, forneça as credenciais de administrador para entrar no Workplace by Facebook. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o Workplace by Facebook manualmente, abra uma nova janela do navegador da Web, entre em seu empresarial do Workplace by Facebook como administrador e execute as seguintes etapas:

    > [!NOTE]
    > Como parte do processo de autenticação SAML, o Workplace poderá utilizar cadeias de consulta de até 2,5 quilobytes para passar parâmetros para o Azure AD.

1. No painel de navegação esquerdo, navegue até a guia **Segurança** > **Autenticação**.

    ![Painel de Administração](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Marque a opção **SSO (logon único)**.
    
    b. Clique em **+Adicionar novo Provedor de SSO**.
    > [!NOTE]
    > Marque também a caixa de seleção Logon de senha. Os administradores podem precisar dessa opção para fazer logon enquanto fazem a sobreposição do certificado, a fim de impedir que fiquem sendo bloqueados.

1. Na guia **Autenticação**, selecione **SSO (logon único)** e execute as seguintes etapas:

    ![Guia Autenticação](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. No **Nome do Provedor de SSO**, insira o nome da instância de SSO, como Azureadsso.

    b. Na caixa de texto **URL do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **URL do Emissor do SAML**, cole o valor do **Identificador do Azure AD**, copiado do portal do Azure.

    d. Abra o **Certificado codificado em Base64** no bloco de notas baixado do Portal do Azure, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado SAML**.

    e. Copie a **URL do Público-alvo** da instância e cole-a na caixa de texto **Identificador (ID da Entidade)** na seção **Configuração Básica do SAML** no portal do Azure.

    f. Copie a **URL do Destinatário** da instância e cole-a na caixa de texto **URL de Logon** na seção **Configuração Básica do SAML** no portal do Azure.

    g. Copie a **URL do ACS (Serviço do Consumidor de Declaração)** da sua instância e cole-a na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    h. Role até o final da seção e clique no botão **Testar SSO**. Isso resultará na exibição de uma janela pop-up com a página de logon do Azure AD apresentada. Insira suas credenciais como de costume para se autenticar.

    **Solução de problemas:** verifique se o endereço de email retornado do Azure AD é o mesmo da conta do Workplace com a qual você está conectado.

    i. Depois que o teste for concluído com êxito, role até a parte inferior da página e clique no botão **Salvar**.

    j. Agora, todos os usuários que usam o Workplace verão a página de logon do Azure AD para autenticação.

1. **Redirecionamento de Logoff SAML (opcional)**  -

    Você pode optar por configurar uma URL de Logoff SAML, que pode ser usada para apontar para a página de logoff do Azure AD. Quando essa configuração for habilitada e configurada, o usuário não será mais direcionado para a página de logoff do Workplace. Em vez disso, o usuário será redirecionado para a URL que foi adicionada à configuração Redirecionamento de Logoff SAML.

### <a name="configuring-reauthentication-frequency"></a>Configurando a frequência de reautenticação

É possível configurar o Workplace para solicitar uma verificação SAML todos os dias, a cada três dias, toda semana, a cada duas semanas, todos os meses ou nunca.

> [!NOTE]
> O valor mínimo para a verificação SAML em aplicativos móveis é definido como uma semana.

Você também pode forçar uma redefinição SAML para todos os usuários usando o botão Exigir autenticação SAML para todos os usuários agora.

### <a name="create-workplace-by-facebook-test-user"></a>Criar um usuário de teste do Workplace by Facebook

Nesta seção, um usuário chamado B.Fernandes será criado no Workplace by Facebook. O Workplace by Facebook dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.

Não há nenhuma ação para você nesta seção. Se um usuário ainda não existir no Workplace by Facebook, um novo será criado quando você tentar acessar o Workplace by Facebook.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Workplace by Facebook](https://www.workplace.com/help/work/).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Workplace by Facebook, na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Workplace by Facebook diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clica no bloco do Workplace by Facebook em Meus Aplicativos, isso o redirecionará para a URL de Logon do Workplace by Facebook. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Testar o SSO para o Workplace by Facebook (móvel)

1. Abra o aplicativo móvel do Workplace by Facebook. Na página de entrada, clique em **FAZER LOGON**.

    ![A entrada](./media/workplacebyfacebook-tutorial/test05.png)

2. Insira seu email comercial e clique em **CONTINUAR**.

    ![O email](./media/workplacebyfacebook-tutorial/test02.png)

3. Clique em **APENAS UMAS VEZ**.

    ![A uma vez](./media/workplacebyfacebook-tutorial/test04.png)

4. Clique em **Permitir**.

    ![O permitir](./media/workplacebyfacebook-tutorial/test03.png)

5. Por fim, após entrar com sucesso, a home page do aplicativo será exibida.    

    ![A home page](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Workplace by Facebook, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
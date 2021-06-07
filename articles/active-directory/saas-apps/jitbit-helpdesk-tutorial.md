---
title: 'Tutorial: Integração do Azure Active Directory com o Jitbit Helpdesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Jitbit Helpdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: b3fbf73ab51092f614a416355477fc552a404fd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581790"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração do Azure Active Directory com o Jitbit Helpdesk

Neste tutorial, você aprenderá a integrar o Jitbit Helpdesk ao Azure AD (Azure Active Directory). Ao integrar o Jitbit Helpdesk ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Jitbit Helpdesk.
* Permitir que os usuários sejam conectados automaticamente ao Jitbit Helpdesk com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Jitbit Helpdesk habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Jitbit Helpdesk é compatível com o SSO iniciado por **SP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>Adicionar o Jitbit Helpdesk por meio da galeria

Para configurar a integração do Jitbit Helpdesk ao Azure AD, você precisará adicionar o Jitbit Helpdesk da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Jitbit Helpdesk** na caixa de pesquisa.
1. Escolha **Jitbit Helpdesk** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Configurar e testar o SSO do Azure AD para o Jitbit Helpdesk

Configure e teste o SSO do Azure AD com o Jitbit Helpdesk usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Jitbit Helpdesk.

Para configurar e testar o SSO do Azure AD com o Jitbit Helpdesk, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Jitbit Helpdesk](#configure-jitbit-helpdesk-sso)** – para configurar as definições de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Jitbit Helpdesk](#create-jitbit-helpdesk-test-user)** – para ter um equivalente de B.Fernandes no Jitbit Helpdesk que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Jitbit Helpdesk**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma das URLs usando o seguinte padrão:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Para obter esse valor, entre em contato com a [equipe de suporte do cliente Jitbit Helpdesk](https://www.jitbit.com/support/).

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://www.jitbit.com/web-helpdesk/`

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar Jitbit Helpdesk**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder acesso ao Jitbit Helpdesk.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Jitbit Helpdesk**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name=&quot;configure-jitbit-helpdesk-sso&quot;></a>Configurar o SSO do Jitbit Helpdesk

1. Em uma janela diferente do navegador da Web, entre no site da sua empresa do Jitbit Helpdesk como administrador.

1. Na barra de ferramentas na parte superior, clique em **Administração**.

    ![Administração](./media/jitbit-helpdesk-tutorial/settings.png &quot;Administração")

1. Clique em **Configurações gerais**.

    ![A captura de tela mostra o link Propriedades Gerais.](./media/jitbit-helpdesk-tutorial/general.png "Usuários, empresas e permissões")

1. Na seção de configuração **Configurações de autenticação** , realize as seguintes etapas:

    ![Configurações de autenticação](./media/jitbit-helpdesk-tutorial/authentication.png "Configurações de autenticação")

    a. Selecione **Habilitar logon único SAML 2.0** para entrar usando SSO (Logon Único) com **OneLogin**.

    b. Na caixa de texto **URL de ponto de extremidade**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra seu certificado codificado em **base 64** no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**

    d. Clique em **Salvar alterações**.

### <a name="create-jitbit-helpdesk-test-user"></a>Criar usuário de teste do Jitbit Helpdesk

Para permitir que os usuários do Azure AD entrem no Jitbit Helpdesk, eles devem ser provisionados no Jitbit Helpdesk. No caso do Jitbit Helpdesk, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu locatário do **Jitbit Helpdesk**.

1. No menu na parte superior, clique em **Administração**.

    ![Administração](./media/jitbit-helpdesk-tutorial/settings.png "Administração")

1. Clique em **Usuários, empresas e permissões**.

    ![Usuários, empresas e permissões](./media/jitbit-helpdesk-tutorial/users.png "Usuários, empresas e permissões")

1. Clique em **Adicionar usuário**.

    ![Adicionar usuário](./media/jitbit-helpdesk-tutorial/add.png "Adicionar usuário")

1. Na seção Criar, digite os dados da conta do Azure AD que deseja provisionar da seguinte maneira:

    ![Criar](./media/jitbit-helpdesk-tutorial/create-section.png "Criar")

   a. Na caixa de texto **Nome de usuário**, digite o nome do usuário como **BrendaFernandes**.

   b. Na caixa de texto **Email**, digite o email do usuário como **BrittaSimon@contoso.com** .

   c. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

   d. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **Fernandes**.

   e. Clique em **Criar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Jitbit Helpdesk ou APIs fornecidas pelo Jitbit Helpdesk para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Jitbit Helpdesk, por meio da qual será possível iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Jitbit Helpdesk e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco Jitbit Helpdesk em Meus Aplicativos, você será redirecionado para a URL de logon do Jitbit Helpdesk. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o Jitbit Helpdesk, você poderá impor o controle de sessão, que protege contra a exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
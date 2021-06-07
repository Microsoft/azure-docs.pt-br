---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Freshservice | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Freshservice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fb8b12cdb8fd9ed37ac4086d213183e800d6febc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651737"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Freshservice

Neste tutorial, você aprenderá a integrar o Freshservice ao Azure AD (Azure Active Directory). Quando integrar o Freshservice ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Freshservice.
* Permitir que os usuários sejam conectados automaticamente ao Freshservice com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Freshservice.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Freshservice dá suporte ao SSO iniciado por **SP**

## <a name="add-freshservice-from-the-gallery"></a>Adicionar o Freshservice por meio da galeria

Para configurar a integração do Freshservice ao Azure AD, você precisa adicionar o Freshservice da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Freshservice** na caixa de pesquisa.
1. Escolha **Freshservice** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Configurar e testar o SSO do Azure AD para o Freshservice

Configure e teste o SSO do Azure AD com o Freshservice usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Freshservice.

Para configurar e testar o SSO do Azure AD com o Freshservice, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Freshservice](#configure-freshservice-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Freshservice](#create-freshservice-test-user)** – para ter um equivalente de B. Fernandes no Freshservice que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Freshservice**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<company-name>.freshservice.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<company-name>.freshservice.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Entre em contato com [equipe de suporte ao Cliente do Freshservice](https://support.freshservice.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Freshservice** no **portal do Azure**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela o acesso ao Freshservice.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Freshservice**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-freshservice-sso"></a>Configurar o SSO do Freshservice

1. Para automatizar a configuração no Freshservice, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o Freshservice** para ser direcionado ao aplicativo Freshservice. Nele, forneça as credenciais de administrador para entrar no Freshservice. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o Freshservice manualmente, faça logon no seu site de empresa do Freshservice como administrador.

1. No menu à esquerda, clique em **Administração** e selecione **Segurança de Helpdesk** em **Configurações Gerais**.

    ![Administrador](./media/freshservice-tutorial/configure-1.png "Admin")

1. Em **Segurança**, clique em **Ir para a seção Segurança do Freshservice 360**.

    ![Segurança](./media/freshservice-tutorial/configure-2.png "Segurança")

1. Na seção **Segurança** , realize as seguintes etapas:

    ![Logon único](./media/freshservice-tutorial/configure-3.png "Logon Único")
  
    a. Para **Logon Único**, selecione **Ativado**.

    b. No **Método de Logon**, selecione **SSO SAML**.

    c. Na caixa de texto **ID da Entidade fornecida pelo IdP**, cole o valor da **ID da Entidade** copiado do portal do Azure.

    d. Na caixa de texto **URL de SSO SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Nas **Opções de Assinatura**, selecione **Apenas Asserções Assinadas** na lista suspensa.

    f. Na caixa de texto **URL de logoff**, cole o **valor do URL de logout**, copiado do portal do Azure.

    g. Na caixa de texto **Certificado de Segurança**, cole o valor **Certificado (Base64)** que você obteve anteriormente.
  
    h. Clique em **Save** (Salvar).


## <a name="create-freshservice-test-user"></a>Criar um usuário de teste do Freshservice

Para permitir que os usuários do Azure AD façam logon no FreshService, eles devem ser provisionados no Freshservice. No caso do FreshService, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site da empresa **FreshService** como administrador.

2. No menu à esquerda, clique em **Admin**.

3. Na seção **Gerenciamento de Usuários**, clique em **Solicitantes**.

    ![Solicitantes](./media/freshservice-tutorial/create-user-1.png "Solicitantes")

4. Clique em **Novo Solicitante**.

    ![Novos Solicitantes](./media/freshservice-tutorial/create-user-2.png "Novos Solicitantes")

5. Na seção **Novo Solicitante**, insira os dados nos campos obrigatórios e clique em **Salvar**.
    ![Novo Solicitante](./media/freshservice-tutorial/create-user-3.png "Novo Solicitante")  

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email que inclui um link para confirmar a conta antes que ela se torne ativa
    >  

    > [!NOTE]
    > É possível usar qualquer outra ferramenta de criação da conta de usuário do FreshService ou APIs fornecidas pelo FreshService para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Freshservice, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Freshservice e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Freshservice em Meus Aplicativos, você será conectado automaticamente ao Freshservice, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

 Após configurar o Freshservice, você poderá impor controles de sessão, que protegem contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
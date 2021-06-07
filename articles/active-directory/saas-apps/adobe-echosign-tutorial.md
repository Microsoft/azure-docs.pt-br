---
title: 'Tutorial: Integração do Azure Active Directory com o Adobe Sign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 7162c38aae2fec4ea21aae56fa8c3649f7a55425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649936"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Integração do Azure Active Directory ao Adobe Sign

Neste tutorial, você aprenderá a integrar o Adobe Sign ao Azure AD (Azure Active Directory). Ao integrar o Adobe Sign ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Adobe Sign.
* Permitir que os usuários entrem automaticamente no Adobe Sign com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:
 
* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Adobe Sign habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Adobe Sign é compatível com o SSO iniciado por **SP**

## <a name="add-adobe-sign-from-the-gallery"></a>Adicionar o Adobe Sign da galeria

Para configurar a integração do Adobe Sign ao Azure AD, você precisa adicionar o Adobe Sign à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Adobe Sign** na caixa de pesquisa.
1. Selecione **Adobe Sign** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Configurar e testar o SSO do Azure AD para o Adobe Sign

Nesta seção, você configura e testa o logon único do Azure AD com o Adobe Sign, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adobe Sign.

Para configurar e testar o logon único do Azure AD com o Adobe Sign, você precisará executar as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Adobe Sign](#configure-adobe-sign-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Adobe Sign](#create-adobe-sign-test-user)** – para ter um equivalente de Brenda Fernandes no Adobe Sign que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Adobe Sign, realize as etapas a seguir:

1. No portal do Azure, na página de integração do aplicativo **Adobe Sign**, selecione **Logon único**.

1. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

1. Na página **Definir logon único com SAML**, clique no ícone lápis para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.echosign.com/`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do Adobe Sign](https://helpx.adobe.com/in/contact/support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Adobe Sign**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Adobe Sign.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Adobe Sign**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-adobe-sign-sso"></a>Configurar o SSO do Adobe Sign

1. Antes da configuração, entre em contato com a [equipe de suporte do Adobe Sign Client](https://helpx.adobe.com/in/contact/support.html) para adicionar seu domínio na lista de permissões do Adobe Sign. Veja como adicionar o domínio:

    a. A equipe de suporte do [ Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html) envia um token gerado aleatoriamente. Para o seu domínio, o token será como o seguinte: **adobe-sign-verification = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publique o token de verificação em um registro de texto DNS e notifique a [ equipe de suporte do Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > Isso pode levar alguns dias ou mais. Observe que os atrasos de propagação de DNS significam que um valor publicado no DNS pode não estar visível por uma hora ou mais. Seu administrador de TI deve ter conhecimento sobre como publicar esse token em um registro de texto DNS.

    c. Quando você notifica a [ equipe de suporte do Adobe Sign Client ](https://helpx.adobe.com/in/contact/support.html) por meio do ticket de suporte, depois que o token é publicado, ele valida o domínio e o adiciona à sua conta.

    d. Geralmente, aqui está como publicar o token em um registro DNS:

    * Entre na sua conta de domínio
    * Encontrar a página para atualizar o registro DNS. Esta página pode ser chamada de Gerenciamento de DNS, Gerenciamento do Servidor de Nomes ou Configurações Avançadas.
    * Encontrar os registros TXT para o domínio.
    * Adicione um registro TXT com o valor total do token fornecido pela Adobe.
    * Salve suas alterações.

1. Em uma janela diferente do navegador da Web, entre no site da empresa Adobe Sign como administrador.

1. No menu SAML, selecione **Configurações da conta** > **Configurações SAML**.

    ![Captura de tela da página Configurações de SAML do Adobe Sign](./media/adobe-echosign-tutorial/settings.png "Conta")

1. Na seção **Configuração do SAML**, execute as seguintes etapas:

    ![Captura de tela que realça as configurações de SAML, incluindo SAML obrigatório.](./media/adobe-echosign-tutorial/saml1.png "Configurações do SAML")

   ![Captura de tela das configurações do SAML](./media/adobe-echosign-tutorial/saml.png "Configurações do SAML")

   a. Em **Modo SAML**, selecione **SAML Obrigatório**.

   b. Selecione **Permitir Administradores da Conta Echosign para efetuar login usando suas Credenciais Echosign**.

   c. Em **Criação de usuário**, selecione **Adicionar automaticamente usuários autenticados por SAML**.

   d. Cole o **Identificador do Azure AD** que você copiou do portal do Azure na caixa de texto **Identidade da entidade do IDP**.

   e. Cole a **URL de logon**, copiada do portal do Azure, na caixa de texto **URL de logon do IDP**.

   f. Cole a **URL de logoff**, copiada do portal do Azure, na caixa de texto **URL de logoff do IDP**.

   g. Abra o arquivo **Certificate (Base64)** baixado no Bloco de Notas. Copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado IdP**.

   h. Selecione **Salvar alterações**.

### <a name="create-adobe-sign-test-user"></a>Criar usuário de teste do Adobe Sign

Para permitir que os usuários do Azure AD entrem no Adobe Sign, eles devem ser provisionados no Adobe Sign. Esta é uma tarefa manual.

>[!NOTE]
>Você pode usar qualquer outra ferramenta ou API de criação de conta de usuário do Adobe Sign fornecida pelo Adobe Sign para aprovisionar contas de usuários do Azure AD. 

1. Faça login no **site da empresa do Adobe Sign** como administrador.

2. No menu no topo, selecione **Conta**. Em seguida, no painel esquerdo, selecione **Usuários e grupos** > **Crie um novo usuário**.

    ![Captura de tela do site da empresa Adobe Sign, com Conta, Usuários e Grupos e Criar usuário realçados](./media/adobe-echosign-tutorial/account.png "Conta")

3. Na seção **Criar Novo Usuário** , realize as seguintes etapas:

    ![Captura de tela da seção Criar usuário](./media/adobe-echosign-tutorial/user.png "Criar Usuário")

    a. Digite **Endereço de e-mail**, **Nome** e **Sobrenome** de uma conta válida do AD do Azure que você deseja provisionar nas caixas de texto relacionadas.

    b. Selecione **Criar usuário**.

>[!NOTE]
>O titular da conta do Active Directory do Azure recebe um e-mail que inclui um link para confirmar a conta antes que ela se torne ativa. 

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Adobe Sign na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Adobe Sign diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Adobe Sign nos Meus Aplicativos, você deverá ser conectado automaticamente ao Adobe Sign para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Adobe Sign, você poderá impor um controle de sessão, que fornece proteção contra o vazamento e a infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
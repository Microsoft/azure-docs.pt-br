---
title: 'Tutorial: integração do Azure Active Directory com o TOPdesk - Secure | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TOPdesk – Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 5ed23889d8648c65ea0887d2f0f3406b50291f12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654283"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Integração do Active Directory do Azure ao TOPdesk - Secure

Neste tutorial, você aprenderá a integrar o TOPdesk – Secure ao Azure AD (Azure Active Directory). Ao integrar o TOPdesk – Secure ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao TOPdesk – Secure.
* Permitir que os usuários entrem automaticamente no TOPdesk – Secure (Logon Único) com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:
 
 * Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do TOPdesk – Secure habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O TOPdesk – Secure dá suporte ao SSO iniciado por **SP**

## <a name="add-topdesk---secure-from-the-gallery"></a>Adicionar o TOPdesk – Secure por meio da galeria

Para configurar a integração do TOPdesk – Secure ao Azure AD, é necessário adicionar o TOPdesk – Secure da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **TOPdesk – Secure** na caixa de pesquisa.
1. Selecione **TOPdesk – Secure** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Configurar e testar o SSO do Azure AD para o TOPdesk – Secure

Nesta seção, você configurará e testará o logon único do Azure AD com o TOPdesk – Secure, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TOPdesk – Secure.

Para configurar e testar o logon único do Azure AD com o TOPdesk – Secure, você precisará executar as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    2. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do TOPdesk – Secure](#configure-topdesk---secure-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do TOPdesk – Secure](#create-topdesk---secure-test-user)** – para ter um equivalente de Brenda Fernandes no TOPdesk – Secure que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TOPdesk – Secure, execute as seguintes etapas:

1. No portal do Azure, na página de integração de aplicativos do **TOPdesk – Secure**, selecione **Logon único**.

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

3. Na página **Definir logon único com SAML**, clique no ícone lápis para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net`

    b. Na caixa **Identificador de URL**, preencha a URL de metadados do TOPdesk que você pode recuperar da configuração do TOPdesk. Ela deve usar o seguinte padrão: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do TOPdesk – Secure](https://www.topdesk.com/us/support/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o TOPdesk – Secure**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao TOPdesk – Secure.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TOPdesk – Secure**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name=&quot;configure-topdesk---secure-sso&quot;></a>Configurar o SSO do TOPdesk – Secure

1. Entrar no seu site da empresa **TOPdesk - Seguro** como um administrador.

2. No menu **TOPdesk**, clique em **Configurações**.

    ![Configurações](./media/topdesk-secure-tutorial/ic790598.png &quot;Configurações")

3. Clique em **Configurações de Logon**.

    ![Configurações de Logon](./media/topdesk-secure-tutorial/ic790599.png "Configurações de Logon")

4. Expanda o menu **Configurações de Logon** e clique em **Geral**.

    ![Geral](./media/topdesk-secure-tutorial/ic790600.png "Geral")

5. Na seção **Seguro** da seção de configuração do **Logon SAML**, execute as etapas a seguir:

    ![Configurações Técnicas](./media/topdesk-secure-tutorial/ic790855.png "Configurações Técnicas")

    a. Clique em **Baixar** para baixar o arquivo de metadados público e, em seguida, salve-o localmente em seu computador.

    b. Abra o arquivo de metadados e, em seguida, localize o nó **AssertionConsumerService**.

    ![Serviço de Declaração do Consumidor](./media/topdesk-secure-tutorial/ic790856.png "Serviço de Declaração do Consumidor")

    c. Copie o valor **AssertionConsumerService**, cole-o na caixa de texto URL de resposta na seção **Domínio e URLs do TOPdesk – Secure**.

6. Execute as seguintes etapas para criar um arquivo de certificado:

    ![Certificado](./media/topdesk-secure-tutorial/ic790606.png "Certificado")

    a. Abra o arquivo de metadados baixado do Portal do Azure.

    b. Expanda o nó **RoleDescriptor** que possui um **xsi:type** de **fed:ApplicationServiceType**.

    c. Copie o valor do nó **X509Certificate**.

    d. Salve o valor **X509Certificate** copiado localmente no computador em um arquivo.

7. Na seção **Pública**, clique em **Adicionar**.

    ![Adicionar](./media/topdesk-secure-tutorial/ic790607.png "Adicionar")

8. Na página de diálogo **Assistente de configuração SAML**, execute as etapas a seguir:

    ![Assistente de configuração do SAML](./media/topdesk-secure-tutorial/ic790608.png "Assistente de configuração do SAML")

    a. Para carregar o arquivo de metadados baixado do Portal do Azure, em **Metadados de Federação**, clique em **Procurar**.

    b. Para carregar o arquivo de certificado em **Certificado (RSA)**, clique em **Procurar**.

    c. Para **chave privada (RSA, PKCS8, DER)**, você pode carregar sua própria chave privada ou contatar [TOPdesk - equipe de suporte do Secure Client](https://www.topdesk.com/us/support) para obter a chave privada.

    d. Para carregar o arquivo de logotipo que você obteve da equipe de suporte do TOPdesk, em **Ícone do logotipo**, clique em **Procurar**.

    e. Na caixa de texto **Atributo de nome de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Na caixa de texto **Nome de exibição**, digite um nome para sua configuração.

    g. Clique em **Save** (Salvar).

### <a name="create-topdesk---secure-test-user"></a>Criar um usuário de teste do TOPdesk – Secure

Para permitir que os usuários do AD do Azure façam logon no TOPdesk - Secure, eles deverão ser provisionados no TOPdesk - Secure.  
No caso do TOPdesk - Secure, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuários, execute as seguintes etapas:

1. Faça logon em seu site de empresa do **TOPdesk - Secure** como administrador.

2. No menu na parte superior, clique em **TOPdesk \> Novo \> Arquivos de Suporte \> Operador**.

    ![Operador](./media/topdesk-secure-tutorial/ic790610.png "Operador")

3. Na caixa de diálogo **Novo Operador**, execute as etapas a seguir:

    ![Novo Operador](./media/topdesk-secure-tutorial/ic790611.png "Operador New")

    a. Clique na guia **Geral**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **Simon**.

    c. Selecione um **Site** para a conta na seção **Local**.

    d. Na caixa de texto **Nome de logon** da seção **Logon do TOPdesk**, digite um nome de logon para o usuário.

    e. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do TOPdesk – Secure ou as APIs fornecidas pelo TOPdesk – Secure para provisionar as contas de usuário do Azure AD.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do TOPdesk – Secure, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do TOPdesk – Secure e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do TOPdesk – Secure em Meus Aplicativos, você será conectado automaticamente ao TOPdesk – Secure, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o TOPdesk – Secure, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
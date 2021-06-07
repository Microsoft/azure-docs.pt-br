---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NetDocuments | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetDocuments.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 48ba2810c0aaf304042580cdf6579df54fd9ccd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645659"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NetDocuments

Neste tutorial, você aprenderá a integrar o NetDocuments ao Azure AD (Azure Active Directory). Com a integração do NetDocuments ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao NetDocuments.
* Permitir que os usuários sejam conectados automaticamente ao NetDocuments usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do NetDocuments habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O NetDocuments é compatível com o SSO iniciado por **SP**

## <a name="adding-netdocuments-from-the-gallery"></a>Adicionar o NetDocuments da galeria

Para configurar a integração do NetDocuments ao Azure AD, você precisará adicionar o NetDocuments da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **NetDocuments** na caixa de pesquisa.
1. Selecione **NetDocuments** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-netdocuments"></a>Configurar e testar o SSO do Azure AD para o NetDocuments

Configure e teste o SSO do Azure AD com o NetDocuments usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NetDocuments.

Para configurar e testar o SSO do Azure AD com o NetDocuments, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do NetDocuments](#configure-netdocuments-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do NetDocuments](#create-netdocuments-test-user)** – para ter um equivalente de B. Fernandes no NetDocuments que esteja vinculado à representação desse usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **NetDocuments**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite um dos seguintes padrões de URL:

    |URL de logon|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma das URLs:

    |Identificador|
    |-----------|
    |`http://netdocuments.com/VAULT`|
    |`http://netdocuments.com/EU`|
    |`http://netdocuments.com/AU`|
    |`http://netdocuments.com/DE`|
    |

    c. Na caixa de texto **URL de Resposta**, digite um dos seguintes padrões de URL:

    |URL de resposta|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de resposta e a URL de logon propriamente ditas. A ID do repositório é um valor que começa com **CA-** seguido por um código de oito caracteres associado a seu repositório do NetDocuments. Você pode conferir o [documento de suporte de identidade federada do NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) para obter mais informações. Como alternativa, você pode contatar a [equipe de suporte ao cliente do NetDocuments](https://support.netdocuments.com/hc/) para obter esses valores se tiver dificuldades para configurar usando as informações acima. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Seu aplicativo NetDocuments espera as declarações do SAML em um formato específico, o que exige adicionar mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo NetDocuments espera que **nameidentifier** seja mapeado com **ObjectID** ou com qualquer outra declaração aplicável à sua Organização como **nameidentifier**. Portanto, é necessário editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, encontre a **URL de Metadados de Federação do Aplicativo** e copie a URL.

    ![O link de download do Certificado](common/copy-metadataurl.png)

1. Na seção **Configurar o NetDocuments**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder a ela o acesso ao NetDocuments.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **NetDocuments**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name=&quot;configure-netdocuments-sso&quot;></a>Configurar o SSO do NetDocuments

1. Em uma janela de navegador da Web diferente, entre no site de sua empresa do NetDocuments como administrador.

2. No canto superior direito, selecione seu nome>**Admin**.

3. Escolha **Central de Segurança**.
   
    ![Repositório](./media/netdocuments-tutorial/security-center.png &quot;Central de Segurança")

4. Selecione **Autenticação Avançada**.
    
    ![Configurar opções de autenticação avançadas](./media/netdocuments-tutorial/advance-authentication.png "Configurar opções de autenticação avançadas")

5.  Na guia **ID Federada**, execute as seguintes etapas:   
   
    [ ![Identidade Federada](./media/netdocuments-tutorial/federated-id.png "Identidade federada")](./media/netdocuments-tutorial/federated-id.png#lightbox)
   
    a. Em **Tipo de servidor de identidade federada**, selecione-o como **Microsoft Azure Active Directory**.
    
    b.  Selecione **Escolher Arquivo** para carregar o arquivo de metadados que você baixou do portal do Azure.
    
    c.  Selecione **SALVAR**.

### <a name="create-netdocuments-test-user"></a>Criar um usuário de teste do NetDocuments

Para permitir que os usuários do Azure AD entrem no NetDocuments, eles devem ser provisionados no NetDocuments. No caso do NetDocuments, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **NetDocuments** como administrador.

2. No canto superior direito, selecione seu nome>**Admin**.
   
    ![Administrador](./media/netdocuments-tutorial/user-admin.png "Admin")

3. Selecione **Usuários e grupos**.
   
    ![Usuários e grupos](./media/netdocuments-tutorial/users-groups.png "Repositório")

4. Na caixa de texto **Endereço de Email**, digite o endereço de email de uma conta válida do Azure Active Directory que você deseja provisionar e clique em **Adicionar Usuário**.
   
    ![Endereço de Email](./media/netdocuments-tutorial/user-mail.png "Endereço de Email")
   
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa. É possível usar qualquer outra ferramenta de criação da conta de usuário do NetDocuments ou as APIs fornecidas pelo NetDocuments para provisionar as contas de usuário do Azure Active Directory.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do NetDocuments, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do NetDocuments e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do NetDocuments em Meus Aplicativos, você será conectado automaticamente ao NetDocuments, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o NetDocuments, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
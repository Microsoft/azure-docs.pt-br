---
title: 'Tutorial: integração do Azure Active Directory com o Greenhouse | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Greenhouse.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: jeedes
ms.openlocfilehash: 6a14b16e34faa827228594bf6d4f0bd9ed48cf72
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221748"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Tutorial: integração do Active Directory do Azure ao Greenhouse

Neste tutorial, você aprenderá a integrar o Greenhouse ao Azure AD (Azure Active Directory). Ao integrar o Greenhouse ao Azure AD, você pode:

* No Azure AD, controle quem tem acesso ao Greenhouse.
* Permitir que seus usuários sejam conectados automaticamente ao Greenhouse com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Greenhouse.

> [!NOTE] 
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Greenhouse dá suporte ao SSO iniciado por **SP e IdP**.

## <a name="adding-greenhouse-from-the-gallery"></a>Adicionando o Greenhouse por meio da galeria

Para configurar a integração do Greenhouse ao Azure AD, é necessário adicionar o Greenhouse à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, insira **Greenhouse** na caixa de pesquisa.
1. Selecione **Greenhouse** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Configurar e testar o SSO do Azure AD para o Greenhouse

Configure e teste o SSO do Azure AD com o Greenhouse usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Greenhouse.

Para configurar e testar o SSO do Azure AD com o Greenhouse, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Greenhouse](#configure-greenhouse-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Greenhouse](#create-greenhouse-test-user)** – para ter um equivalente de Brenda Fernandes no Greenhouse que esteja vinculado à representação de usuário do Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Greenhouse**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<COMPANYNAME>.greenhouse.io`

    b. Na caixa de texto **URL de Resposta**, digite uma URL nos seguintes padrões:
    
    | URL de resposta|
    | -------------- |
    | `https://<COMPANYNAME>.greenhouse.io/users/saml/consume` |
    | `https://app.greenhouse.io/<ENTITY ID>/users/saml/consume` |
    |

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<COMPANYNAME>.greenhouse.io`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do Greenhouse](https://www.greenhouse.io/contact) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Greenhouse**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Greenhouse.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Greenhouse**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-greenhouse-sso"></a>Configurar o SSO do Greenhouse

1. Em outra janela do navegador da Web, entre no site do Greenhouse como administrador.

1. Acesse **Configurar > Dev Center > Logon Único**.

    ![captura de tela da página de SSO](./media/greenhouse-tutorial/configure.png)

1. Execute as etapas a seguir na página **Logon Único**.

    ![captura de tela da página de configuração de SSO](./media/greenhouse-tutorial/sso-page.png)

    a. Copie o valor da **URL do Consumidor de Declaração do SSO** e cole esse valor na caixa de texto **URL de Resposta** na seção **Configuração Básica do SAML** no portal do Azure.

    b. Na caixa de texto **ID da Entidade/Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logon Único**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    d. Abra o **XML de Metadados de Federação** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Impressão Digital de Certificado IdP**.

    e. Insira o valor do **Formato de Identificador de Nome** na lista suspensa.

    f. Clique em **Iniciar o Teste**.

    >[!NOTE]
    >Como alternativa, você também pode carregar o arquivo **XML de Metadados de Federação** clicando na opção **Escolher Arquivo**.

### <a name="create-greenhouse-test-user&quot;></a>Criar um usuário de teste do Greenhouse

Para permitir que os usuários do Azure AD façam logon no Greenhouse, eles devem ser provisionados no Greenhouse. No caso do Greenhouse, o provisionamento é uma tarefa manual.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Greenhouse ou as APIs fornecidas pelo Greenhouse para provisionar as contas de usuário do Azure AD. 

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Entre no site da empresa **Greenhouse** como um administrador.

2. Acesse **Configurar > Usuários > Novos Usuários**
   
    ![Usuários](./media/greenhouse-tutorial/create-user-1.png &quot;Usuários")

4. Na seção **Adicionar Novos Usuários**, execute as etapas a seguir:
   
    ![Adicionar Novo Usuário](./media/greenhouse-tutorial/create-user-2.png "Adicionar Novo Usuário")

    a. Na caixa de texto **Inserir emails do usuário**, digite o endereço de email de uma conta válida do Active Directory do Azure que você quer provisionar.

    b. Clique em **Save** (Salvar).    
   
      >[!NOTE]
      >O titular da conta do Active Directory do Azure recebe um email com um link para confirmar a conta antes que ela se torne ativa.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Greenhouse, na qual você poderá iniciar o fluxo de logon.  

* Acesse a URL de Entrada do Greenhouse diretamente e inicie o fluxo de logon desse local.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no Greenhouse, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Greenhouse em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no Greenhouse, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).



## <a name="next-steps"></a>Próximas etapas

Após configurar o Greenhouse, será possível impor o controle de sessão, que protegerá contra a exfiltração e a infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
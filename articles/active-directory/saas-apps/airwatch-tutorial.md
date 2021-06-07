---
title: 'Tutorial: Integração do Azure Active Directory com AirWatch | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AirWatch.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 4955062e6f0d0c231d09964c985df12284e3733c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653279"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integrar o AirWatch ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o AirWatch ao Azure AD (Azure Active Directory). Quando você integrar o AirWatch ao Azure AD, será possível:

* Controlar no Azure AD quem terá acesso ao AirWatch.
* Permitir que seus usuários entrem automaticamente no AirWatch com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:
 
* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do AirWatch habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* O AirWatch dá suporte ao SSO iniciado por **SP**.

## <a name="add-airwatch-from-the-gallery"></a>Adicionar o AirWatch por meio da galeria

Para configurar a integração do AirWatch com o Azure AD, você precisa adicionar o AirWatch à sua lista de aplicativos SaaS gerenciados a partir da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AirWatch** na caixa de pesquisa.
1. Selecione **AirWatch** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>Configurar e testar o SSO do Azure AD para o AirWatch

Configure e teste o SSO do Azure AD com o AirWatch usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do AirWatch.

Para configurar e testar o SSO do Azure AD com o AirWatch, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do AirWatch](#configure-airwatch-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do AirWatch](#create-airwatch-test-user)** – para ter um equivalente de B.Fernandes no AirWatch que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **AirWatch**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Na caixa de texto **Identificador (ID da Entidade)** , digite um valor como: `AirWatch`

    > [!NOTE]
    > Esse valor não é o real. Atualize esse valor com a URL de Entrada real. Para obter esse valor, entre em contato com a [equipe de suporte do cliente AirWatch](https://www.air-watch.com/company/contact-us/). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo AirWatch espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

1. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | Nome |  Atributo de Origem|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

1. Na página **Configurar logon único com SAML**, na seção **Certificado de assinatura SAML**, localize **XML de Metadados de Federação** e selecione **Download** para baixar o XML de metadados e salve-o em seu computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o AirWatch**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao AirWatch.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **AirWatch**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-airwatch-sso"></a>Configurar SSO do AirWatch

1. Em outra janela do navegador da Web, entre como um administrador em seu site da empresa AirWatch.

1. Na página de configurações. Selecione **Configurações > Enterprise Integration > Serviços de Diretório**.

   ![Configurações](./media/airwatch-tutorial/services.png "Configurações")

1. Clique na guia **Usuário**, na caixa de texto **DN Base**, digite seu nome de domínio e clique em **Salvar**.

   ![Captura de tela que realça a caixa de texto DN de Base.](./media/airwatch-tutorial/user.png "Usuário")

1. Clique na guia **Servidor** .

   ![Servidor](./media/airwatch-tutorial/directory.png "Servidor")

1. Execute as seguintes etapas na seção **LDAP**:

    ![Captura de tela que mostra as alterações que você precisa fazer na seção LDAP.](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. Para **Tipo de Diretório**, selecione **Nenhum**.

    b. Selecione **Usar SAML para Autenticação**.

1. Na seção **SAML 2.0**, para carregar o certificado baixado, clique em **Carregar**.

    ![Upload](./media/airwatch-tutorial/uploads.png "Carregar")

1. Na seção **Solicitar** , realize as seguintes etapas:

    ![Seção de solicitação](./media/airwatch-tutorial/request.png "Solicitação")  

    a. Para **Tipo de Associação de Solicitação**, selecione **POST**.

    b. No portal do Azure, na página da caixa de diálogo **Configurar logon único no AirWatch**, copie o valor da **URL de Logon** e, em seguida, cole-o na caixa de texto **URL de Logon Único do Provedor de Identidade**.

    c. Para **Formato de NameID**, selecione **Endereço de Email**.

    d. Para **Segurança da Solicitação de Autenticação**, selecione **Nenhuma**.

    e. Clique em **Save** (Salvar).

1. Clique na guia **Usuário** novamente.

    ![Usuário](./media/airwatch-tutorial/users.png "Usuário")

1. Na seção **Atributo** , realize as seguintes etapas:

    ![Atributo](./media/airwatch-tutorial/attributes.png "Atributo")

    a. Na caixa de texto **Identificador de Objeto**, digite `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Na caixa de texto **Nome de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Na caixa de texto **Nome de Exibição**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Na caixa de texto **Nome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Na caixa de texto **Sobrenome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Clique em **Save** (Salvar).

### <a name="create-airwatch-test-user"></a>Criar um usuário de teste do AirWatch

Para permitir que os usuários do Azure AD se conectem ao AirWatch, eles devem ser provisionados no AirWatch. No caso do AirWatch, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Entre como administrador em seu site da empresa do **AirWatch**.

2. No painel de navegação à esquerda, clique em **Contas** e em **Usuários**.
  
   ![Usuários](./media/airwatch-tutorial/accounts.png "Usuários")

3. No menu **Usuários**, clique em **Exibição de Lista** e em **Adicionar > Adicionar Usuário**.
  
   ![Captura de tela que realça os botões Adicionar e Adicionar Usuário.](./media/airwatch-tutorial/add.png "Adicionar usuário")

4. No diálogo **Adicionar/Editar Usuário** , realize as seguintes etapas:

   ![Adicionar Usuário](./media/airwatch-tutorial/save.png "Adicionar usuário")

   a. Digite **Nome**, **Senha**, **Confirmar Senha**, **Nome**, **Sobrenome** e **Endereço de Email** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

   b. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do AirWatch ou as APIs fornecidas pelo AirWatch para provisionar as contas de usuário do Azure AD.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do AirWatch, na qual será possível iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do AirWatch e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do AirWatch em Meus Aplicativos, isso redirecionará você à URL de logon do AirWatch. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o AirWatch, você poderá impor controles de sessão, que protegem contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SAP Cloud for Customer | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Cloud for Customer.
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
ms.openlocfilehash: 60381c7d8c452277b53e1af67ae7fc85349521c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735667"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SAP Cloud for Customer

Neste tutorial, você aprenderá a integrar o SAP Cloud for Customer ao Azure AD (Azure Active Directory). Ao integrar o SAP Cloud for Customer ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SAP Cloud for Customer.
* Permitir que os usuários sejam conectados automaticamente ao SAP Cloud for Customer com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SAP Cloud for Customer.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SAP Cloud for Customer dá suporte a SSO iniciado por **SP**

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Adição do SAP Cloud for Customer da galeria

Para configurar a integração do SAP Cloud for Customer ao Azure AD, você precisará adicionar o SAP Cloud for Customer da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SAP Cloud for Customer** na caixa de pesquisa.
1. Selecione **SAP Cloud for Customer** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-for-customer"></a>Configurar e testar o SSO do Azure AD para o SAP Cloud for Customer

Configure e teste o SSO do Azure AD com o SAP Cloud for Customer usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Cloud for Customer.

Para configurar e testar o SSO do Azure AD com o SAP Cloud for Customer, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SAP Cloud for Customer](#configure-sap-cloud-for-customer-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)** – para ter um equivalente de B.Fernandes no SAP Cloud for Customer que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SAP Cloud for Customer**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<server name>.crm.ondemand.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo SAP Cloud for Customer espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

    ![Captura de tela que mostra a caixa de diálogo "Atributos de Usuário" com o ícone "Editar" selecionado.](common/edit-attribute.png)

1. Na seção **Atributos do Usuário** da caixa de diálogo **Declarações e Atributos do Usuário**, realize as seguintes etapas:

    a. Clique no **ícone Editar** para abrir a caixa de diálogo **Gerenciar declarações do usuário**.

    ![Captura de tela que mostra "Atributos e Declarações do Usuário" com o ícone "Editar" selecionado.](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selecione **Transformação** como **origem**.

    c. Na lista **Transformação**, selecione **ExtractMailPrefix()** .

    d. Na lista **Parâmetro 1**, selecione o atributo de usuário que você deseja usar na implementação.
    Por exemplo, se você quiser usar EmployeeID como identificador exclusivo de usuário e tiver armazenado o valor do atributo em ExtensionAttribute2, selecione user.extensionattribute2.

    e. Clique em **Save** (Salvar).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o SAP Cloud for Customer**, copie as URLs apropriadas com base nos seus requisitos.

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

Nesta seção, você permite que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao SAP Cloud for Customer.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Cloud for Customer**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sap-cloud-for-customer-sso"></a>Configurar o SSO do SAP Cloud for Customer

1. Abra uma nova janela do navegador da Web e entre no site da empresa do SAP Cloud for Customer como administrador.

2. No lado esquerdo do menu, clique em **Provedores de Identidade** > **Provedores de Identidade Corporativos** > **Adicionar** e, na janela pop-up, adicione o nome do Provedor de identidade, como **Azure AD**, clique em **Salvar** e selecione **Configuração de SAML 2.0**.

    ![Captura de tela que mostra a página "Provedores de Identidade" com a caixa de texto de diálogo "Adicionar Provedor de Identidade" realçada e o botão "Salvar" selecionado.](./media/sap-customer-cloud-tutorial/configure01.png)

3. Na seção **Configuração do SAML 2.0**, execute as seguintes etapas:

    ![Captura de tela que mostra a janela "Configurações do SAML 2.0" com o botão "Navegar" selecionado.](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Clique em **Procurar** para fazer upload do arquivo XML de Metadados de Federação que você baixou do portal do Azure.

    b. Depois que o arquivo XML for carregado com êxito, os valores abaixo serão preenchidos automaticamente e, em seguida, clique em **Salvar**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Criar um usuário de teste do SAP Cloud for Customer

Para permitir que os usuários do Azure AD entrem no SAP Cloud for Customer, eles devem ser provisionados no SAP Cloud for Customer. No SAP Cloud for Customer, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no SAP Cloud for Customer como um Administrador de Segurança.

2. No lado esquerdo do menu, clique em **Usuários e Autorizações** > **Gerenciamento de Usuários** > **Adicionar Usuário**.

    ![Captura de tela que mostra a página "Gerenciamento de Usuários" com o botão "Adicionar Usuário" selecionado.](./media/sap-customer-cloud-tutorial/configure03.png)

3. Na seção **Adicionar Novo Usuário**, realize as seguintes etapas:

    ![Configuração do SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Na caixa de texto **Nome**, insira o nome do usuário como **B**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **Fernandes**.

    c. Na caixa de texto **Email**, insira o email do usuário como `B.Simon@contoso.com`.

    d. Na caixa de texto **Nome de Logon**, insira o nome do usuário como **B.Fernandes**.

    e. Selecione **Tipo de Usuário** de acordo com seus requisitos.

    f. Selecione a opção **Ativação de Conta** de acordo com seus requisitos.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de logon do SAP Cloud for Customer, na qual você poderá iniciar o fluxo de logon. 

* Acesse a URL de logon do SAP Cloud for Customer diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clica no bloco SAP Cloud for Customer nos Meus Aplicativos, isso redirecionará para a URL de logon do SAP Cloud for Customer. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o SAP Cloud for Customer, você poderá impor controles de sessão, que protegem contra a exfiltração e a infiltração de dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
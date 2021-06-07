---
title: 'Tutorial: integração do Azure Active Directory com o TalentLMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TalentLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 84d50d19a8356418a5cbf1f93784e5b8816d4be5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689344"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: integração do Azure Active Directory com o TalentLMS

Neste tutorial, você aprenderá a integrar o TalentLMS ao Azure AD (Azure Active Directory). Quando você integrar o TalentLMS ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao TalentLMS.
* Permitir que os usuários sejam conectados automaticamente ao TalentLMS com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o TalentLMS, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único do TalentLMS.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O TalentLMS é compatível com o SSO iniciado por **SP**

## <a name="add-talentlms-from-the-gallery"></a>Adicionar o TalentLMS da galeria

Para configurar a integração do TalentLMS com o Azure AD, é necessário adicionar o TalentLMS da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **TalentLMS** na caixa de pesquisa.
1. Selecione **TalentLMS** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Configurar e testar o SSO do Azure AD para o TalentLMS

Configure e teste o SSO do Azure AD com o TalentLMS usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TalentLMS.

Para configurar e testar o SSO do Azure AD com o TalentLMS, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do TalentLMS](#configure-talentlms-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do TalentLMS](#create-talentlms-test-user)** – para ter um equivalente de B.Fernandes no TalentLMS que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **TalentLMS**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e URLs do TalentLMS](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.TalentLMSapp.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do TalentLMS](https://www.talentlms.com/contact) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

6. Na seção **Certificado de Autenticação SAML**, copie a **IMPRESSÃO DIGITAL** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

7. Na seção **Configurar o TalentLMS**, copie a URL apropriada, de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao TalentLMS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TalentLMS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name=&quot;configure-talentlms-sso&quot;></a>Configurar o SSO do TalentLMS

1. Em outra janela do navegador da Web, entre no site da empresa TalentLMS como administrador.

1. Na seção **Conta e Configurações**, clique na guia **Usuários**.

    ![Conta e Configurações](./media/talentlms-tutorial/IC777296.png &quot;Conta e Configurações")

1. Clique em **Logon Único (SSO)**.

1. Na seção de Configurações de Logon Único, execute as seguintes etapas:

    ![Logon único](./media/talentlms-tutorial/saml.png "Logon único")

    a. Na lista **Tipo de integração de SSO**, selecione **SAML 2.0**.

    b. Na caixa de texto **ID do Provedor de Identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Cole o valor da **Impressão digital** do Portal do Azure na caixa de texto **Impressão digital do certificado**.

    d.  Na caixa de texto **URL de logon remoto**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Na caixa de texto **URL de logoff remoto**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    f. Preencha o seguinte:

    * Na caixa de texto **TargetedID**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Na caixa de texto **Nome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Na caixa de texto **Sobrenome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Clique em **Save** (Salvar).

### <a name="create-talentlms-test-user"></a>Criar um usuário de teste do TalentLMS

Para permitir que os usuários do Azure AD entre no TalentLMS, eles devem ser provisionados no TalentLMS. No caso do TalentLMS, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu locatário do **TalentLMS**.

1. Clique em **Usuários** e em **Adicionar Usuário**.

1. Na página da caixa de diálogo **Adicionar Usuário**, execute as seguintes etapas:

    ![Adicionar Usuário](./media/talentlms-tutorial/IC777299.png "Adicionar usuário")  

    a. Na caixa de texto **Nome**, insira o nome do usuário como `Britta`.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como `Simon`.
 
    c. Na caixa de texto **Endereço de email**, insira o email do usuário como `brittasimon@contoso.com`.

    d. Clique em **Adicionar Usuário**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do TalentLMS ou as APIs fornecidas pelo TalentLMS para provisionar as contas de usuário do Azure AD.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso vai redirecionar você à URL de Logon do TalentLMS na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do TalentLMS diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do TalentLMS em Meus Aplicativos, isso vai redirecionar você à URL de Logon do TalentLMS. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o TalentLMS, você poderá impor controles de sessão, que fornecem proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
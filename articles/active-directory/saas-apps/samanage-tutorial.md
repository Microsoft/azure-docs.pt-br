---
title: 'Tutorial: Integração do Azure Active Directory com o SolarWinds Service Desk (anteriormente conhecido como Samanage) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SolarWinds Service Desk (anteriormente conhecido como Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 6dcd5612bd2c5957ae0a397c3463dbb42445a754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956036"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Tutorial: Integração do Azure Active Directory ao SolarWinds Service Desk (anteriormente conhecido como Samanage)

Neste tutorial, você aprenderá a integrar o SolarWinds ao Azure AD (Azure Active Directory). Ao integrar o SolarWinds ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SolarWinds.
* Permitir que os usuários sejam conectados automaticamente ao SolarWinds com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SolarWinds.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SolarWinds dá suporte a SSO iniciado por **SP**.

## <a name="add-solarwinds-from-the-gallery"></a>Adicionar o SolarWinds da galeria

Para configurar a integração do SolarWinds ao Azure AD, você precisará adicionar o SolarWinds da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SolarWinds** na caixa de pesquisa.
1. Selecione **SolarWinds** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Configurar e testar o SSO do Azure AD para o SolarWinds

Configure e teste o SSO do Azure AD com o SolarWinds com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SolarWinds.

Para configurar e testar o SSO do Azure AD com o SolarWinds, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SolarWinds](#configure-solarwinds-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SolarWinds](#create-solarwinds-test-user)** – para ter um equivalente de B.Fernandes no SolarWinds que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **SolarWinds**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Logon e o Identificador reais, que são explicados adiante no tutorial. Para obter mais detalhes, contate a [equipe de suporte ao Cliente do Samanage](https://www.samanage.com/support). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o SolarWinds**, copie a URL apropriada, de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao SolarWinds.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SolarWinds**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

<a name=&quot;configure-solarwinds-single-sign-on&quot;></a>

## <a name=&quot;configure-solarwinds-sso&quot;></a>Configurar o SSO do SolarWinds

1. Em outra janela do navegador da Web, faça logon no site corporativo do SolarWinds como administrador.

2. Clique em **Painel** e selecione **Configuração** no painel de navegação à esquerda.
   
    ![Painel](./media/samanage-tutorial/tutorial-samanage-1.png &quot;Painel")

3. Clique em **Logon Único**.
   
    ![Logon único](./media/samanage-tutorial/tutorial-samanage-2.png "Logon Único")

4. Navegue até a seção **Logon usando SAML** , execute as seguintes etapas:
   
    ![Logon usando SAML](./media/samanage-tutorial/tutorial-samanage-3.png "Logon usando SAML")
 
    a. Clique em **Habilitar o Logon Único com SAML**.  
 
    b. Na caixa de texto **URL do Provedor de Identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.    
 
    c. Confirme se a **URL de Logon** corresponde à **URL de Logon** da seção **Configuração Básica de SAML** no portal do Azure.
 
    d. Na caixa de texto **URL de Logoff**, insira o valor da **URL de Logoff** copiado do portal do Azure.
 
    e. Na caixa de texto **Emissor SAML**, digite o URI da ID do aplicativo definido no provedor de identidade.
 
    f. Abra o certificado codificado em Base64 baixado no portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Cole o Certificado x.509 do Provedor de Identidade abaixo**.
 
    g. Clique em **Criar usuários se eles não existirem no SolarWinds**.
 
    h. Clique em **Atualizar**.

### <a name="create-solarwinds-test-user"></a>Criar usuário de teste do SolarWinds

Para que os usuários do Azure AD possam fazer logon no SolarWinds, eles devem ser provisionados no SolarWinds.  
No caso do SolarWinds, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site corporativo do SolarWinds como administrador.

2. Clique em **Painel** e selecione **Configuração** no painel de navegação à esquerda.
   
    ![Instalação](./media/samanage-tutorial/tutorial-samanage-1.png "Instalação")

3. Clique na guia **Usuários**
   
    ![Usuários](./media/samanage-tutorial/tutorial-samanage-6.png "Usuários")

4. Clique em **Novo usuário**.
   
    ![Novo usuário](./media/samanage-tutorial/tutorial-samanage-7.png "Novo Usuário")

5. Digite o **Nome** e o **Endereço de Email** de uma conta do Azure Active Directory que você deseja provisionar e clique em **Criar usuário**.
   
    ![Criar Usuário](./media/samanage-tutorial/tutorial-samanage-8.png "Criar Usuário")
   
   >[!NOTE]
   >O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa. Você pode usar qualquer outra ferramenta de criação de conta de usuário do SolarWinds ou as APIs fornecidas pelo SolarWinds para provisionar contas de usuário do Azure Active Directory.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do SolarWinds, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do SolarWinds e inicie o fluxo de logon.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do SolarWinds em Meus Aplicativos, será redirecionado à URL de Logon do SolarWinds. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o SolarWinds, você poderá impor o controle de sessão, que protege contra a exfiltração e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
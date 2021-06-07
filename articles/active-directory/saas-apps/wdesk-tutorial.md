---
title: 'Tutorial: Integração do Azure Active Directory com o Wdesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104603209"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Wdesk

Neste tutorial, você aprenderá a integrar o Wdesk ao Azure AD (Azure Active Directory). Ao integrar o Wdesk ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Wdesk.
* Permitir que os usuários sejam conectados automaticamente ao Wdesk com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Wdesk.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Wdesk dá suporte ao SSO iniciado por **SP** e **IdP**.

## <a name="add-wdesk-from-the-gallery"></a>Adicionar o Wdesk por meio da galeria

Para configurar a integração do Wdesk ao Azure AD, você precisará adicionar o Wdesk a partir da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Wdesk** na caixa de pesquisa.
1. Selecione **Wdesk** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Configurar e testar o SSO do Azure AD para o Wdesk

Nesta seção, você configurará e testará o logon único do Azure AD com o Wdesk, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Wdesk.

Para configurar e testar o SSO do Azure AD com o Wdesk, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Wdesk](#configure-wdesk-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Wdesk](#create-wdesk-test-user)** : para ter um equivalente de B.Fernandes no Wdesk que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Wdesk**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Você pode obter esses valores no portal do WDesk ao configurar o SSO.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Wdesk**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Wdesk.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Wdesk**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-wdesk-sso"></a>Configurar o SSO do Wdesk

1. Em outra janela do navegador da Web, entre no Wdesk como Administrador de segurança.

1. No canto inferior esquerdo, clique em **Administrador** e escolha **Conta de Administrador**:
 
    ![A captura de tela mostra o Administrador da Conta selecionado no menu Administrador.](./media/wdesk-tutorial/account.png)

1. Na Administração do Wdesk, navegue até **Segurança**, **SAML** > **Configurações de SAML**:

    ![A captura de tela mostra a opção Configurações do SAML selecionada na guia SAML.](./media/wdesk-tutorial/settings.png)

1. Em **Configurações de ID de Usuário SAML**, verifique se a **ID de Usuário SAML é o Nome de Usuário do Wdesk**.

    ![A captura de tela mostra a opção Configurações de ID de Usuário SAML, em que você pode selecionar a ID de Usuário SAML é o Nome de Usuário do Wdesk.](./media/wdesk-tutorial/wdesk-username.png)

4. Em **Configurações Gerais**, marque **Habilitar Logon Único do SAML**:

    ![A captura de tela mostra a opção Editar Configurações do SAML, em que você pode selecionar a opção Habilitar Logon Único do SAML.](./media/wdesk-tutorial/user-settings.png)

5. Em **Detalhes do Provedor de Serviço**, execute as seguintes etapas:

    ![A captura de tela mostra os Detalhes do Provedor de Serviço, em que você pode inserir os valores descritos.](./media/wdesk-tutorial/service-provider.png)

    1. Copie a **URL de Logon** e cole-a na caixa de texto **Url de Logon** no Portal do Azure.

    1. Copie a **URL de Metadados** e cole-a na caixa de texto **Identificador** no Portal do Azure.

    1. Copie a **URL de Logon** e cole-a na caixa de texto **Url de Resposta** no Portal do Azure.

    1. Clique em **Salvar** no Portal do Azure para salvar as alterações.      

1. Clique em **Definir Configurações do IdP** para abrir a caixa de diálogo **Editar Configurações do IdP**. Clique em **Escolher Arquivo** para localizar o arquivo **Metadata.xml** salvo no Portal do Azure, depois carregue-o.
    
    ![A captura de tela mostra a opção Editar Configurações de IdP, em que você pode carregar metadados.](./media/wdesk-tutorial/metadata.png)
  
1. Clique em **Salvar alterações**.

    ![A captura de tela mostra o botão Salvar alterações.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Criar um usuário de teste do Wdesk

Para permitir que os usuários do Azure AD entrem no Wdesk, eles precisam ser provisionados no Wdesk. No Wdesk, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Wdesk como Administrador de segurança.

2. Navegue até **Administrador** > **Conta de Administrador**.

     ![A captura de tela mostra o Administrador da Conta selecionado no menu Administrador.](./media/wdesk-tutorial/account.png)

3. Clique em **Membros**, sob **Pessoas**.

4. Agora clique em **Adicionar Membro** para abrir a caixa de diálogo **Adicionar Membro**. 
   
    ![A captura de tela mostra a guia Membros, em que você pode selecionar Adicionar Membro.](./media/wdesk-tutorial/create-user-1.png)  

5. Na caixa de texto **Usuário**, insira o nome do usuário, como b.simon@contoso.com e clique no botão **Continuar**.

    ![A captura de tela mostra a caixa de diálogo Adicionar Membro, em que você pode inserir um usuário.](./media/wdesk-tutorial/create-user-3.png)

6.  Insira os detalhes, conforme mostrado abaixo:
  
    ![A captura de tela mostra a caixa de diálogo Adicionar Membro, em que você pode adicionar Informações Básicas para um usuário.](./media/wdesk-tutorial/create-user-4.png)
 
    a. Na caixa de texto **Email**, insira o email do usuário como b.simon@contoso.com.

    b. Na caixa de texto **Nome**, insira o nome do usuário como **B**.

    c. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

7. Clique no botão **Salvar Membro**.  

    ![A captura de tela mostra a opção Enviar email de boas-vindas com o botão Salvar Membro.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Wdesk, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Wdesk e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no Wdesk, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Wdesk em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no Wdesk, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Wdesk, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

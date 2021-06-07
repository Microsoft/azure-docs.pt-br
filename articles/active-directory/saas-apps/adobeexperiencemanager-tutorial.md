---
title: 'Tutorial: integração do Azure Active Directory com o Adobe Experience Manager | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 1a0340b9d8971446113862465c67143674bd5668
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653347"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: integração do Azure Active Directory com o Adobe Experience Manager

Neste tutorial, você aprenderá como integrar o Adobe Experience Manager ao Azure AD (Azure Active Directory). Quando você integra o Adobe Experience Manager ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Adobe Experience Manager.
* Permitir que os usuários entrem automaticamente no Adobe Experience Manager com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Adobe Experience Manager.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Adobe Experience Manager dá suporte ao SSO iniciado por **SP e IDP**

* O Adobe Experience Manager dá suporte ao provisionamento de usuário **Just-in-Time**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adicionando o Adobe Experience Manager da galeria

Para configurar a integração do Adobe Experience Manager ao Azure AD, será necessário adicionar o Adobe Experience Manager da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Adobe Experience Manager** na caixa de pesquisa.
1. Selecione **Adobe Experience Manager** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Configurar e testar o SSO do Azure AD para o Adobe Experience Management

Configure e teste o SSO do Azure AD com o Adobe Experience Manager usando um usuário de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adobe Experience Management.

Para configurar e testar o SSO do Azure AD com o Adobe Experience Manager, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Adobe Experience Manager](#configure-adobe-experience-manager-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    1. **[Criar um usuário de teste do Adobe Experience Manager](#create-adobe-experience-manager-test-user)** – para ter um equivalente de Brenda Fernandes no Adobe Experience Manager que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Adobe Experience Manager**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite um valor exclusivo definido também no servidor do AEM.

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize o valor da URL de Resposta com a URL de Resposta real. Para obter esse valor, contate a [equipe de suporte ao Cliente do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL de servidor do Adobe Experience Manager.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Adobe Experience Manager**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure, concedendo acesso ao Adobe Experience Manager.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Adobe Experience Manager**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-adobe-experience-manager-sso"></a>Configurar o SSO do Adobe Experience Manager

1. Abra o portal de administração do **Adobe Experience Manager** em outra janela do navegador.

2. Selecione **Configurações** > **Segurança** > **Usuários**.

    ![Captura de tela que mostra o bloco Usuários no Adobe Experience Manager.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Selecione **Administrador** ou qualquer outro usuário relevante.

    ![Captura de tela que realça o usuário Administrator.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Selecione **Configurações da conta** > **Gerenciar TrustStore**.

    ![Captura de tela que mostra Gerenciar TrustStore em Configurações da conta.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. Em **Adicionar Certificado do arquivo CER**, clique no botão **Selecionar Arquivo de Certificado**. Navegue até o arquivo de certificado que você baixou do Portal do Azure e selecione-o.

    ![Captura de tela que realça o botão Selecionar Arquivo de Certificado.](./media/adobe-experience-manager-tutorial/user-2.png)

6. O certificado é adicionado ao TrustStore. Observe o alias do certificado.

    ![Captura de tela que mostra que o certificado é adicionado ao TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. Na página **Usuários**, selecione **authentication-service**.

    ![Captura de tela que realça o serviço de autenticação na tela.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Selecione **Configurações de conta** > **Criar/gerenciar repositório de chaves**. Criar repositório de chaves fornecendo uma senha.

    ![Captura de tela que realça Gerenciar KeyStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Volte para a tela de administrador. Em seguida, selecione **Configurações** > **Operações** > **Console Web**.

    ![Captura de tela que realça o Console Web em Operações na seção Configurações.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Isso abre a página de configuração.

    ![Configurar o botão salvar do logon único](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Localize **Manipulador de autenticação do Adobe Granite SAML 2.0**. Selecione então o ícone **Adicionar**.

    ![Captura de tela que realça o Manipulador de Autenticação do Adobe Granite SAML 2.0.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Siga as ações a seguir nesta página.

    ![Botão Salvar em Configurar Logon Único](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. Na caixa **Caminho**, digite **/** .

    b. Na caixa **URL do IDP**, insira o valor da **URL de Logon** copiado do portal do Azure.

    c. No **Alias do certificado IDP**, digite o valor do **Alias do Certificado** que você adicionou na TrustStore.

    d. Na caixa **ID da Entidade Fornecida pela Segurança**, insira o valor exclusivo do **Identificador do Azure AD** configurado no portal do Azure.

    e. Na caixa **URL do Serviço do Consumidor de Declaração**, digite o valor da **URL de Resposta** que você configurou no Portal do Azure.

    f. Na caixa **Senha do Repositório de Chaves**, digite a **Senha** que você definiu no repositório de chaves.

    g. Na caixa **ID do Atributo de Usuário**, insira a **ID do Nome** ou outra ID de usuário relevante em seu caso.

    h. Selecione **Criar Usuários CRX Automaticamente**.

    i. Na caixa **URL de Logoff**, insira o valor exclusivo da **URL de Logoff** obtido no portal do Azure.

    j. Clique em **Salvar**.

### <a name="create-adobe-experience-manager-test-user"></a>Criar um usuário de teste do Adobe Experience Manager

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Adobe Experience Manager. Se você selecionar a opção **Criar Usuários CRX Automaticamente**, os usuários serão criados automaticamente após a autenticação bem-sucedida.

Se você desejar criar usuários manualmente, trabalhe com a [equipe de suporte do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para adicionar os usuários na plataforma do Adobe Experience Manager.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Adobe Experience Manager, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Adobe Experience Manager e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Adobe Experience Manager para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Adobe Experience Manager em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Adobe Experience Manager para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Adobe Experience Manager, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
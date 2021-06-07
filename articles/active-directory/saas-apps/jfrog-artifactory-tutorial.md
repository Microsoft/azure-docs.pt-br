---
title: 'Tutorial: Integração do Azure Active Directory ao JFrog Artifactory | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o JFrog Artifactory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: b943be684d84e1e193d9318e9f1c6423dcd38795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648910"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Tutorial: Integre o JFrog Artifactory com o Azure Active Directory

Neste tutorial, você aprenderá como integrar o JFrog Artifactory ao Azure AD (Azure Active Directory). Ao integrar o JFrog Artifactory com o Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao JFrog Artifactory.
* Permitir que os usuários sejam conectados automaticamente ao JFrog Artifactory com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do JFrog Artifactory habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O JFrog Artifactory dá suporte ao SSO iniciado por **SP e IdP**.
* O JFrog Artifactory dá suporte ao provisionamento de usuário **Just-In-Time**.

## <a name="add-jfrog-artifactory-from-the-gallery"></a>Adicionar o JFrog Artifactory por meio da galeria

Para configurar a integração do JFrog Artifactory ao Azure AD, você precisa adicionar o JFrog Artifactory da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **JFrog Artifactory** na caixa de pesquisa.
1. Selecione **JFrog Artifactory** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>Configurar e testar o SSO do Azure AD para o JFrog Artifactory

Configure e teste o SSO do Azure AD com o JFrog Artifactory empregando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do JFrog Artifactory.

Para configurar e testar o SSO do Azure AD com o JFrog Artifactory, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do JFrog Artifactory](#configure-jfrog-artifactory-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do JFrog Artifactory](#create-jfrog-artifactory-test-user)** – para ter um equivalente do usuário B.Fernandes no JFrog Artifactory, que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **JFrog Artifactory**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `<servername>.jfrog.io`

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:
    
    - Para o Artifactory 6.x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Para o Artifactory 7.x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:
    - Para o Artifactory 6.x: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Para o Artifactory 7.x: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do JFrog Artifactory](https://support.jfrog.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo JFrog Artifactory espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados em sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos do Usuário.

    ![Captura de tela que mostra Atributos do Usuário com o controle de edição em destaque.](common/edit-attribute.png)

1. Além do indicado acima, o JFrog Artifactory espera que mais alguns atributos sejam transmitidos novamente na resposta SAML. Na seção **Atributos e Declarações de Usuário** na caixa de diálogo **Declarações de Grupo (Versão Prévia)** , execute as seguintes etapas:

    a. Clique na **caneta** ao lado de **Grupos retornados na declaração**.

    ![Captura de tela que mostra Atributos e Declarações do Usuário, com o ícone Editar selecionado.](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![A captura de tela mostra a seção Declarações de Grupo com a opção Todos os grupos selecionada.](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. Selecione **Todos os Grupos** na lista de opções.

    c. Clique em **Save** (Salvar).

4. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Configure o Artifactory (nome do provedor de serviços SAML) com o campo "Identificador" (confira a etapa 4). Na seção **Configurar o JFrog Artifactory**, copie as URLs apropriadas de acordo com as suas necessidades.

   - Para o Artifactory 6.x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Para o Artifactory 7.x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao JFrog Artifactory.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **JFrog Artifactory**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-jfrog-artifactory-sso"></a>Configurar SSO do JFrog Artifactory

Para configurar o logon único no lado do **JFrog Artifactory**, é necessário enviar o **Certificado (bruto)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do JFrog Artifactory](https://support.jfrog.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-jfrog-artifactory-test-user"></a>Criar usuário de teste do JFrog Artifactory

Nesta seção, será criado um usuário chamado B.Fernandes no JFrog Artifactory. O JFrog Artifactory é compatível com o provisionamento de usuário Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no JFrog Artifactory, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do JFrog Artifactory, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do JFrog Artifactory e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no JFrog Artifactory, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do JFrog Artifactory em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no JFrog Artifactory, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o JFrog Artifactory, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
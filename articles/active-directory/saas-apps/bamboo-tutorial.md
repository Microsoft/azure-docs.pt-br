---
title: 'Tutorial: Integração do Azure Active Directory com o SSO do SAML para Bamboo da Resolution GmbH | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e SSO do SAML para Bamboo da Resolution GmbH.
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
ms.openlocfilehash: 8ec834d76692f78d1d7bc60ddbd4c73fe4adaede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646094"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Tutorial: Integração do Azure Active Directory com o SSO do SAML para Bamboo da Resolution GmbH

Neste tutorial, você aprenderá a integrar o SSO do SAML para Bamboo da resolution GmbH ao Azure AD (Azure Active Directory). Ao integrar o SSO do SAML para Bamboo da resolution GmbH ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SSO do SAML para Bamboo da resolution GmbH.
* Permitir que os usuários sejam conectados automaticamente ao SSO do SAML para Bamboo da resolution GmbH com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SSO de SAML para Bamboo da Resolution GmbH, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do SSO do SAML para Bamboo da Resolution GmbH

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SSO do SAML para Bamboo da resolution GmbH dá suporte ao SSO iniciado por **SP e IDP**
* O SSO do SAML para Bamboo da resolution GmbH dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="add-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Adicionar o SSO do SAML para Bamboo da resolution GmbH por meio da galeria

Para configurar a integração do SSO do SAML para Bamboo da Resolution GmbH no Azure AD, adicione o SSO do SAML para Bamboo da Resolution GmbH da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SSO do SAML para Bamboo da resolution GmbH** na caixa de pesquisa.
1. Selecione **SSO do SAML para Bamboo da resolution GmbH** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Configurar e testar o SSO do Azure AD com o SSO do SAML para Bamboo da resolution GmbH

Configure e teste o SSO do Azure AD com o SSO do SAML para Bamboo da resolution GmbH usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação vinculada entre um usuário do Azure AD e o usuário relacionado no SSO do SAML para Bamboo da resolution GmbH.

Para configurar e testar o SSO do Azure AD com o SSO do SAML para Bamboo da resolution GmbH, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
     1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
     1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do SAML para Bamboo da resolution GmbH](#configure-saml-sso-for-bamboo-by-resolution-gmbh-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SSO do SAML para Bamboo da resolution GmbH](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** : para ter um equivalente de Brenda Fernandes no SSO do SAML para Bamboo da resolution GmbH que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o SSO do Azure AD no portal do Azure.
 
1. No portal do Azure, na página de integração de aplicativos do **SSO do SAML para Bamboo da resolution GmbH**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um Método de Logon Único**, selecione **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)
4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

     Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao Cliente do SSO do SAML para Bamboo da Resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o SSO do SAML para Bamboo da Resolution GmbH**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de seleção **Mostrar senha** e, em seguida, anote a senha.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao SSO do SAML para Bamboo da resolution GmbH.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSO do SAML para Bamboo da resolution GmbH**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-sso"></a>Configurar o SSO do SAML para Bamboo da resolution GmbH

1. Faça logon no site da empresa do SAML SSO para Bamboo da Resolution GmbH como administrador.

1. No lado direito da barra de ferramentas principal, clique em **configurações** > **complementos**.

    ![As configurações](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Vá para a seção SEGURANÇA, clique em **logon único do SAML** na barra de menus.

    ![O Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Na **página de configuração do plug-in de logon único do SAML**, clique em **Adicionar idp**.

    ![Adicionar idp](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Na página **Escolher seu Provedor de Identidade SAML**, execute as seguintes etapas:

    ![O provedor de identidade](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Selecione **Tipo de Idp** como **AD do AZURE**.

    b. Na caixa de texto **Nome**, digite um nome.

    c. Na caixa de texto **Descrição**, digite a descrição.

    d. Clique em **Próximo**.

1. Na página **Configuração do provedor de identidade**, clique no botão **Avançar**.

    ![As configurações de identidade](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Na página **importar metadados de Idp do SAML**, clique em **carregar arquivo** para carregar o arquivo **METADATA XML** que você fez o download do portal do Azure.

    ![O idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Clique em **Próximo**.

1. Clique em **Salvar alterações**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Criar um usuário de teste do SSO do SAML para Bamboo da Resolution GmbH

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no SSO do SAML para Bamboo da Resolution GmbH. O SSO do SAML para Bamboo da Resolution GmbH dá suporte ao provisionamento just-in-time e os usuários podem ser criados manualmente, entre em contato com a [equipe de suporte ao cliente do SSO do SAML para Bamboo da Resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support), de acordo com as suas necessidades.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do SSO do SAML para Bamboo da resolution GmbH, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do SSO do SAML para Bamboo da resolution GmbH e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao SSO do SAML para Bamboo da resolution GmbH, para o qual configurou o SSO.

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do SSO do SAML para Bamboo da resolution GmbH em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao SSO do SAML para o Bamboo da resolution GmbH, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SSO do SAML para Bamboo da resolution GmbH, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
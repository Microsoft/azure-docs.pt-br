---
title: 'Tutorial: Integração do Azure Active Directory com o MOVEit Transfer - Azure AD integration | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MOVEit Transfer - Azure AD integration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653015"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Integração do Azure Active Directory com o MOVEit Transfer - Azure AD integration

Neste tutorial, você aprenderá a integrar o MOVEit Transfer – Azure AD ao Azure AD (Azure Active Directory). Ao integrar o MOVEit Transfer – Azure AD ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso à integração MOVEit Transfer – Azure AD.
* Permitir que os seus usuários entrem automaticamente na integração MOVEit Transfer –Azure AD usando as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura da integração MOVEit Transfer – Azure AD habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Dá suporte ao **SP** SSO iniciado do MOVEit Transfer - Azure AD integration

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Adicionar integração MOVEit Transfer – Azure AD por meio da galeria

Para configurar a integração do MOVEit Transfer - Azure AD integration ao Azure AD, você precisa adicionar o MOVEit Transfer - Azure AD integration pela galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Integração MOVEit Transfer – Azure AD** na caixa de pesquisa.
1. Selecione **Integração MOVEit Transfer – Azure AD** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Configurar e testar a integração MOVEit Transfer – Azure AD para SSO do Azure AD

Configure e teste o SSO do Azure AD com a integração MOVEit Transfer – Azure AD usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na integração MOVEit Transfer – Azure AD.

Para configurar o SSO do Azure AD com a integração MOVEit Transfer – Azure AD, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO da integração MOVEit Transfer – Azure AD](#configure-moveit-transfer---azure-ad-integration-sso)** – definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste da integração MOVEit Transfer – Azure AD](#create-moveit-transfer---azure-ad-integration-test-user)** – para ter um equivalente de B.Fernandes na integração MOVEit Transfer – Azure AD vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Integração MOVEit Transfer – Azure AD**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **Responder à URL** serão preenchidos automaticamente na seção **Configuração Básica do SAML**:

    ![Informações de logon único de Domínio e URLs do MoVEit Transfer - Azure AD integration](common/sp-identifier-reply.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://contoso.com`

    > [!NOTE]
    > O valor da **URL de logon** não é real. Atualize o valor com a URL de Logon real. Entre em contato com a equipe de suporte ao cliente do [MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support) para obter o valor. Você pode baixar o **arquivo de metadados do provedor de serviço** da **URL de metadados do provedor de serviço** que é explicada posteriormente na seção do tutorial **Configurar logon único do MOVEit Transfer - Azure AD integration**. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar MoVEit Transfer - Azure AD integration**, copie as URLs apropriadas de acordo com seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso à integração MOVEit Transfer – Azure AD.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **MOVEit Transfer - Azure AD integration**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>Configurar o SSO da integração MOVEit Transfer – Azure AD

1. Faça logon em seu locatário do MOVEit Transfer como administrador.

2. No painel de navegação esquerdo, clique em **Configurações**.

    ![Seção Configurações no lado do aplicativo](./media/moveittransfer-tutorial/settings.png)

3. Clique no link **Logon Único** que está em **Políticas de Segurança -> Autenticação de Usuário**.

    ![Políticas de segurança no lado do aplicativo](./media/moveittransfer-tutorial/sso.png)

4. Clique no link de URL de Metadados para baixar o documento de metadados.

    ![URL de metadados do provedor de serviço](./media/moveittransfer-tutorial/metadata.png)
    
   * Verifique se **entityID** corresponde a **identificador** na seção **Configuração básica de SAML**.
   * Verifique se a URL de local **AssertionConsumerService** corresponde à **URL DE RESPOSTA** na seção **Configuração básica de SAML**.
    
     ![Configurar o logon único no lado do aplicativo](./media/moveittransfer-tutorial/xml.png)

5. Clique no botão **Adicionar Provedor de Identidade** para adicionar um novo Provedor de Identidade Federada.

    ![Adicionar Provedor de Identidade](./media/moveittransfer-tutorial/idp.png)

6. Clique em **Procurar...** para selecionar o arquivo de metadados que você baixou do portal do Azure e clique em **Adicionar Provedor de Identidade** para carregar o arquivo baixado.

    ![Provedor de identidade SAML](./media/moveittransfer-tutorial/saml.png)

7. Selecione "**Sim**" como **Habilitado** na página **Editar Provedor de Identidade Federado...** e clique em **Salvar**.

    ![Configurações de Provedor de Identidade Federado](./media/moveittransfer-tutorial/save.png)

8. Na página **Editar Configurações de usuário de Provedor de Identidade Federado**, realize as ações a seguir:
    
    ![Edite Configurações de Provedor de Identidade Federado](./media/moveittransfer-tutorial/attributes.png)
    
    a. Selecione **SAML NameID** como **Nome de logon**.
    
    b. Selecione **Outros** como **Nome completo** e, na caixa de texto **Nome do atributo**, coloque o valor: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selecione **Outros** como **Email** e, na caixa de texto **Nome do atributo**, coloque o valor: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selecione **Sim** para **Criação automática de conta no momento da conexão**.
    
    e. Clique no botão **Salvar** .

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Crie um usuário de teste do MOVEit Transfer - Azure AD integration

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no MOVEit Transfer - Azure AD integration. O MOVEit Transfer - Azure AD integration dá suporte ao provisionamento just-in-time, que você habilitou. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o MOVEit Transfer - Azure AD integration, caso ele ainda não exista.

>[!NOTE]
>Se precisar criar um usuário manualmente, você precisará contatar a [equipe de suporte do MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Entrada da integração MOVEit Transfer – Azure AD, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Entrada da integração MOVEit Transfer – Azure AD e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco da integração MOVEit Transfer – Azure AD em Meus Aplicativos, você deverá entrar automaticamente na integração MOVEit Transfer – Azure AD para a qual você definiu o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar a integração MOVEit Transfer – Azure AD, você poderá impor o controle de sessão, que protege contra a exfiltração e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
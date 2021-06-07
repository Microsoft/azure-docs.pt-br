---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Firewall de Aplicativo Web do FortiWeb   Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Firewall de Aplicativo Web do FortiWeb.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731929"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Firewall de Aplicativo Web do FortiWeb

Neste tutorial, você aprenderá a integrar o Firewall de Aplicativo Web do FortiWeb ao Azure AD (Azure Active Directory). Ao integrar o Firewall de Aplicativo Web do FortiWeb ao Azure AD, você pode:

* Controlar quem tem acesso ao Firewall de Aplicativo Web do FortiWeb no Azure AD.
* Permitir que usuários sejam conectados de modo automático ao Firewall de Aplicativo Web do FortiWeb com as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Firewall de Aplicativo Web do FortiWeb.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Firewall de Aplicativo Web do FortiWeb dá suporte ao SSO iniciado por **SP**.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>Como adicionar o Firewall de Aplicativo Web do FortiWeb na galeria

Para configurar a integração do Firewall de Aplicativo Web do FortiWeb ao Azure AD, será necessário adicionar à sua lista de aplicativos SaaS gerenciados o Firewall de Aplicativo Web do FortiWeb na galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar na galeria**, digite **Firewall de Aplicativo Web do FortiWeb** na caixa de pesquisa.
1. Selecione **Firewall de Aplicativo Web do FortiWeb** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Configurar e testar o SSO do Azure AD para o Firewall de Aplicativo Web do FortiWeb

Configure e teste o SSO do Azure AD usando o Firewall de Aplicativo Web do FortiWeb com um usuário de teste chamado **B.Fernandes**. É necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente no Firewall de Aplicativo Web do FortiWeb para que o SSO funcione.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o Firewall de Aplicativo Web do FortiWeb:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Firewall de Aplicativo Web do FortiWeb](#configure-fortiweb-web-application-firewall-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Firewall de Aplicativo Web do FortiWeb](#create-fortiweb-web-application-firewall-test-user)** – Para ter uma contraparte de B.Fernandes no Firewall de Aplicativo Web do FortiWeb que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Firewall de Aplicativo Web do FortiWeb**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

   a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://www.<CUSTOMER_DOMAIN>.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.<CUSTOMER_DOMAIN>.com`

    d. Na caixa de texto **URL de Logoff**, digite uma URL usando o seguinte padrão: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > O `<FORTIWEB_NAME>` é um identificador de nome que será usado posteriormente ao fornecer a configuração ao FortiWeb.
    > Entre em contato com a [equipe de suporte do Firewall de Aplicativo Web do FortiWeb](mailto:support@fortinet.com) para obter os valores reais de URL. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)


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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure, concedendo acesso ao Firewall de Aplicativo Web do FortiWeb.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Firewall de Aplicativo Web do FortiWeb**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-fortiweb-web-application-firewall-sso"></a>Configurar o SSO do Firewall de Aplicativo Web do FortiWeb

1.  Navegue para `https://<address>:8443`, em que `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiWeb.

2.  Entre usando as credenciais de administrador fornecidas durante a implantação da VM do FortiWeb.

1. Execute as etapas abaixo na página a seguir.

    ![Página do servidor SAML](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  No menu à esquerda, clique em **Usuário**.

    b.  Em Usuário, clique em **Servidor Remoto**.

    c.  Clique em **Servidor SAML**.

    d.  Clique em **Criar Novo**.

    e.  No campo **Nome**, forneça o valor para `<fwName>` usado na seção Configurar o Azure AD.

    f.  Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    g. Ao lado de **Metadados**, clique em **Escolher arquivo** e selecione o arquivo **XML de Metadados de Federação** que você baixou do portal do Azure.

    h.  Clique em **OK**.

### <a name="create-a-site-publishing-rule"></a>Criar uma regra de publicação do site

1.  Navegue para `https://<address>:8443`, em que `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiWeb.

1.  Entre usando as credenciais de administrador fornecidas durante a implantação da VM do FortiWeb.
1. Execute as etapas abaixo na página a seguir.

    ![Regra de publicação do site](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  No menu à esquerda, clique em **Entrega de Aplicativos**.
    
    b.  Em **Entrega de Aplicativos**, clique em **Publicação do Site**.
    
    c.  Em **Publicação do Site**, clique em **Publicação do Site**.
    
    d.  Clique em **Regra de Publicação do Site**.
    
    e.  Clique em **Criar Novo**.
    
    f.  Forneça um nome para a regra de publicação do site.
    
    g.  Ao lado de **Tipo de Site Publicado**, clique em **Expressão Regular**.
    
    i.  Ao lado de **Site Publicado**, forneça uma cadeia de caracteres que corresponda ao cabeçalho de host do site da Web que você está publicando.
    
    j.  Ao lado de **Caminho**, forneça um /.
    
    k.  Ao lado de **Método de Autenticação de Cliente**, selecione **Autenticação SAML**.
    
    l.  Na lista suspensa **Servidor SAML**, selecione o Servidor SAML que você criou anteriormente.
    
    m.  Clique em **OK**.


### <a name="create-a-site-publishing-policy"></a>Criar uma política de publicação do site

1.  Navegue para `https://<address>:8443`, em que `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiWeb.

2.  Entre usando as credenciais de administrador fornecidas durante a implantação da VM do FortiWeb.

1. Execute as etapas abaixo na página a seguir.

    ![Política de publicação do site](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  No menu à esquerda, clique em **Entrega de Aplicativos**.

    b.  Em **Entrega de Aplicativos**, clique em **Publicação do Site**.

    c.  Em **Publicação do Site**, clique em **Publicação do Site**.

    d.  Clique em **Política de Publicação do Site**.

    e.  Clique em **Criar Novo**.

    f.  Forneça um nome para a Política de Publicação do Site.

    g.  Clique em **OK**.

    h.  Clique em **Criar Novo**.

    i.  Na lista suspensa **Regra**, selecione a regra de publicação de site que você criou anteriormente.

    j.  Clique em **OK**.

### <a name="create-and-assign-a-web-protection-profile"></a>Criar e atribuir um Perfil de Proteção da Web

1.  Navegue para `https://<address>:8443`, em que `<address>` é o FQDN ou o endereço IP público atribuído à VM do FortiWeb.

2.  Entre usando as credenciais de administrador fornecidas durante a implantação da VM do FortiWeb.
3.  No menu à esquerda, clique em **Política**.
4.  Em **Política**, clique em **Perfil de Proteção da Web**.
5.  Clique em **Proteção Embutida Padrão** e clique em **Clonar**.
6.  Forneça um nome para o novo perfil de proteção da Web e clique em **OK**.
7.  Selecione o novo perfil de proteção da Web e clique em **Editar**.
8.  Ao lado de **Publicação do Site**, selecione a política de publicação do site que você criou anteriormente.
9.  Clique em **OK**.
 
    ![publicação do site](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. No menu à esquerda, clique em **Política**.
11. Em **Política**, clique em **Política de Servidor**.
12. Selecione a política de servidor usada para publicar o site da Web no qual você deseja usar o Azure Active Directory para autenticação.
13. Clique em **Editar**.
14. Na lista suspensa **Perfil de Proteção da Web**, selecione o perfil de proteção da Web que você acabou de criar.
15. Clique em **OK**.
16. Tentativa de acessar a URL externa na qual o FortiWeb publica o site. Você deve ser redirecionado para o Azure Active Directory para autenticação.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>Criar o usuário de teste do Firewall de Aplicativo Web do FortiWeb

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Firewall de Aplicativo Web do FortiWeb. Trabalhe com a [equipe de suporte do Firewall de Aplicativo Web do FortiWeb](mailto:support@fortinet.com) para adicionar os usuários à plataforma de Firewall de Aplicativo Web do FortiWeb. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado para a URL de Entrada do Aplicativo Web do FortiWeb, na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Entrada do Aplicativo Web do FortiWeb diretamente e inicie o fluxo de logon desse local.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Aplicativo Web do FortiWeb nos Meus Aplicativos, será redirecionado para a URL de Logon do Aplicativo Web do FortiWeb. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o Firewall de Aplicativo Web do FortiWeb, será possível impor o controle de sessão, que protegerá contra a exfiltração e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
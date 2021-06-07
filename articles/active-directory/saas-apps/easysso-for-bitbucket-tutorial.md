---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EasySSO for BitBucket | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EasySSO for BitBucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 56f2eea9dc485c69b6070fda6e9519887f7b30cb
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519507"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EasySSO for BitBucket

Neste tutorial, você aprenderá a integrar o SSO do EasySSO for BitBucket ao Azure AD (Azure Active Directory). Ao integrar o EasySSO for BitBucket ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao EasySSO for BitBucket.
* Permitir que seus usuários entrem automaticamente no EasySSO for BitBucket com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do EasySSO for BitBucket que esteja habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EasySSO for BitBucket dá suporte ao SSO iniciado por SP e IdP.
* O EasySSO for BitBucket dá suporte ao provisionamento de usuário "just-in-time".

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Adicionar o EasySSO for BitBucket por meio da galeria

Para configurar a integração do EasySSO for BitBucket ao Azure AD, é necessário adicionar o EasySSO for BitBucket à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **EasySSO for BitBucket** na caixa de pesquisa.
1. Escolha **EasySSO for BitBucket** nos resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>Configurar e testar o SSO do Azure AD para o EasySSO for BitBucket

Configure e teste o SSO do Azure AD com o EasySSO for BitBucket usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação vinculada entre um usuário do Azure AD e o usuário relacionado do EasySSO for BitBucket.

Para configurar e testar o SSO do Azure AD com o EasySSO for BitBucket, execute estas etapas:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
1. [Configurar o SSO do EasySSO for BitBucket](#configure-easysso-for-bitbucket-sso) para definir as configurações de logon único no lado do aplicativo.
    1. [Criar um usuário de teste do EasySSO for BitBucket](#create-an-easysso-for-bitbucket-test-user) para ter um equivalente de B.Fernandes no EasySSO for BitBucket que esteja vinculado à representação de usuário do Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **EasySSO for BitBucket**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o Logon Único com SAML, com o ícone de lápis realçado](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IdP**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL que usa o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Na caixa de texto **URL de Resposta**, digite uma URL que use o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Selecione **Definir URLs adicionais** e execute a seguinte etapa caso deseje configurar o aplicativo no modo iniciado por **SP**:

    - Na caixa de texto **URL de Entrada**, digite uma URL que usa o seguinte padrão: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do EasySSO](mailto:support@techtime.co.nz) para obter esses valores em caso de dúvida. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo EasySSO for BitBucket espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela de atributos padrão](common/default-attributes.png)

1. O aplicativo EasySSO for BitBucket também espera que mais alguns atributos sejam transmitidos novamente na resposta SAML. A tabela a seguir mostra esses atributos. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome | Atributo de origem|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn: oid:2.5.4.4 | user.surname |
    | urn: oid:2.5.4.42 | user.givenname |
    
    Caso os usuários do Azure AD tenham o **sAMAccountName** configurado, mapeie **urn:oid:0.9.2342.19200300.100.1.1** para o atributo **sAMAccountName**.
    
1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione os links de download do **Certificado (Base64)** ou das opções de **XML de Metadados de Federação**. Salve um ou ambos no computador. Você precisará deles mais tarde para configurar o BitBucket EasySSO.

    ![Captura de tela da seção Certificado de Autenticação SAML, com os links de download realçados](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Se você pretende configurar o EasySSO for BitBucket manualmente com um certificado, copie também a **URL de Logon** e o **Identificador do Azure AD** e salve-os no computador.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste, B.Fernandes, no portal do Azure.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. Para **Nome**, insira `B.Simon`.
   1. Para **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e anote a senha.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao EasySSO for BitBucket.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **EasySSO for BitBucket**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários** e clique em **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Configurar o SSO do EasySSO for BitBucket

1. Para automatizar a configuração no Zoom, é necessário instalar a **extensão do navegador My Apps Secure Sign-in** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, clique em **Configurar o Zoom** e você será direcionado ao aplicativo Zoom. Nele, forneça as credenciais de administrador para entrar no Zoom. A extensão do navegador vai configurar automaticamente o aplicativo e automatizar as etapas de 3 a 10.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Zoom manualmente, em uma janela diferente do navegador da Web, entre em seu site da empresa do Zoom como administrador.

1. Vá até a seção **Administração**.

    ![Captura de tela da instância do BitBucket, com o ícone de engrenagem realçado](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Localize e selecione **EasySSO**.

    ![Captura de tela da opção EasySSO](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Selecione **SAML**. Isso levará você para a seção de configuração do SAML.

    ![Captura de tela da página Administração do EasySSO com o SAML realçado](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Selecione a guia **Certificados** e a seguinte tela será exibida:

    ![Captura de tela da guia Certificados, com várias opções realçadas](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Localize o **Certificado (Base64)** ou o **Arquivo de Metadados** que você salvou na seção anterior deste tutorial. Execute uma das seguintes ações:

    - Use o **Arquivo de Metadados** de Federação do Aplicativo que você baixou em um arquivo local no computador. Selecione o botão de opção **Carregar** e siga o caminho específico para o seu sistema operacional.

    - Abra o **Arquivo de Metadados** de Federação do Aplicativo para ver o conteúdo do arquivo em qualquer editor de texto sem formatação. Copie-o para a área de transferência. Selecione **Entrada** e cole o conteúdo da área de transferência no campo de texto.
 
    - Faça uma configuração totalmente manual. Abra o **Certificado (Base64)** de Federação do Aplicativo para ver o conteúdo do arquivo em qualquer editor de texto sem formatação. Copie-o para a área de transferência e cole-o no campo de texto **Certificados de Autenticação de Tokens do IdP**. Em seguida, acesse a guia **Geral** e preencha os campos **URL de Associação POST** e **ID da Entidade** com os respectivos valores da **URL de Logon** e do **Identificador do Azure AD** salvos anteriormente.
 
1. Selecione **Salvar** na parte inferior da página. Você verá que o conteúdo dos arquivos de metadados ou certificado é analisado nos campos de configuração. A configuração do EasySSO for BitBucket foi concluída.

1. Para testar a configuração, acesse a guia **Aparência** e selecione **Botão de Logon do SAML**. Isso habilitará um botão separado na tela de credenciais do BitBucket, especificamente para testar a integração do SAML do Azure AD de ponta a ponta. Deixe esse botão ativado e configure também o posicionamento, a cor e a conversão no modo de produção.

    ![Captura de tela da guia Aparência da página SAML, com o Botão de Logon do SAML realçado](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Caso tenha algum problema, entre em contato com a [equipe de suporte do EasySSO](mailto:support@techtime.co.nz).

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Criar um usuário de teste do EasySSO for BitBucket

Nesta seção, você criará um usuário chamado Brenda Fernandes no BitBucket. O EasySSO for BitBucket dá suporte ao provisionamento de usuário just-in-time, que está desabilitado por padrão. Para habilitá-lo, marque explicitamente a opção **Criar usuário após logon bem-sucedido** na seção **Geral** da configuração de plug-in do EasySSO. Se um usuário ainda não existir no BitBucket, ele será criado após a autenticação.

No entanto, se você não quiser habilitar o provisionamento automático de usuário quando o usuário entrar pela primeira vez, será necessário ter usuários nos diretórios de usuários utilizados pela instância do BitBucket. Por exemplo, esse diretório pode ser o LDAP ou o Atlassian Crowd.

![Captura de tela da seção Geral da configuração de plug-in do EasySSO, com a opção Criar usuário após logon bem-sucedido realçada](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do EasySSO for BitBucket, na qual poderá iniciar o fluxo de logon.

* Acesse a URL de Logon do EasySSO for BitBucket diretamente e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao EasySSO for BitBucket, para o qual configurou o SSO

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando clicar no bloco do EasySSO for BitBucket no portal Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao EasySSO for BitBucket, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o EasySSO for BitBucket, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
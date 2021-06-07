---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao monday.com | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o monday.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 06f4ddff9c897858eec27f97f8e45a3d656e951d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650780"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao monday.com

Neste tutorial, você aprenderá a integrar o monday.com ao Azure Active Directory (Azure AD). Ao integrar o monday.com ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao monday.com.
* Permitir que os usuários sejam conectados automaticamente ao monday.com com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do monday.com habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O monday.com dá suporte ao SSO iniciado por **SP e IDP**
* O monday.com dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="add-mondaycom-from-the-gallery"></a>Adicionar monday.com por meio da galeria

Para configurar a integração do monday.com ao Azure AD, você precisa adicionar o monday.com da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **monday.com** na caixa de pesquisa.
1. Selecione **monday.com** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-mondaycom"></a>Configurar e testar o SSO do Azure AD para o monday.com

Configure e teste o SSO do Azure AD com o monday.com usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do monday.com.

Para configurar e testar o SSO do Azure AD com o monday.com, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do monday.com](#configure-mondaycom-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Crie um usuário de teste do monday.com](#create-mondaycom-test-user)** – para ter um equivalente de B.Fernandes vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **monday.com**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, se você tiver um **Arquivo de metadados do Provedor de Serviço** e quiser configurar o modo iniciado por **IDP**, execute as etapas a seguir:

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção "Configuração Básica do SAML".

    > [!Note]
    > Se os valores de **Identificador** e **URL de Resposta** não forem preenchidos automaticamente, preencha-os manualmente. O **Identificador** e a **URL de Resposta** são os mesmos e o valor está no seguinte padrão: `https://<your-domain>.monday.com/saml/saml_callback`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<YOUR_DOMAIN>.monday.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do monday.com](https://monday.com/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo monday.com espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela que mostra Atributos e Declarações do Usuário com valores padrão, como Givenname user.givenname e Emailaddress User.mail.](common/default-attributes.png)

1. Além do indicado acima, o aplicativo monday.com espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem |
    |--|--|
    | Email | user.mail |
    | Nome | user.givenname |
    | LastName | user.surname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o monday.com**, copie as URLs apropriadas com base em suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao monday.com.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **monday.com**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-mondaycom-sso"></a>Configurar SSO monday.com

1. Para automatizar a configuração com o monday.com, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Instalação do monday.com**. Você será direcionado ao aplicativo monday.com. Em seguida, forneça as credenciais de administrador para entrar no monday.com. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Se quiser configurar o monday.com manualmente, abra uma nova janela do navegador da Web, entre no monday.com como administrador e execute as seguintes etapas:

1. Vá até **Perfil** no canto superior direito da página e clique em **Administrador**.

    ![A captura de tela mostra o Perfil de administrador selecionado.](./media/mondaycom-tutorial/configuration-1.png)

1. Selecione **Segurança** e clique em **Abrir** ao lado de SAML.

    ![A captura de tela mostra a guia Segurança com a opção de Abrir ao lado de SAML.](./media/mondaycom-tutorial/configuration-2.png)

1. Preencha os detalhes abaixo de seu IDP.

    ![A captura de tela mostra o provedor SAML em que você pode inserir informações de seu IdP.](./media/mondaycom-tutorial/configuration-3.png)

    > [!NOTE]
    > Para saber mais, confira [este](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) artigo

### <a name="create-mondaycom-test-user"></a>Criar usuário de teste do monday.com

Nesta seção, será criado um usuário chamado B.Fernandes no monday.com. O monday.com é compatível com o provisionamento Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no monday.com, um novo será criado quando você tentar acessar o monday.com.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do monday.com, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do monday.com e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao monday.com para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do monday.com em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao monday.com para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o monday.com, é possível impor um controle de sessão, que fornece proteção contra o vazamento e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
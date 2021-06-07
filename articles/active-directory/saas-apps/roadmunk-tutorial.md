---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Roadmunk'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Roadmunk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: a7f4682be2f7fbf308aba32768efa932f27b7a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181678"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Roadmunk

Neste tutorial, você aprenderá a integrar o Roadmunk com o Azure AD (Azure Active Directory). Com a integração do Roadmunk com o Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao Roadmunk.
* Permitir que os usuários sejam conectados automaticamente ao Roadmunk usando as contas do Azure AD deles.
* gerenciar suas contas em um local central, o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Roadmunk que está habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

O Roadmunk dá suporte a SSO iniciado pelo *SP* (provedor de serviços) e pelo *IDP* (provedor de identidade).

## <a name="add-roadmunk-from-the-gallery"></a>Adicionar o Roadmunk por meio da galeria

Para integrar o Roadmunk ao Azure AD, na galeria, adicione o Roadmunk à sua lista de aplicativos SaaS gerenciados:

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Roadmunk** na caixa de pesquisa.
1. Selecione **Roadmunk** nos resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Configurar e testar o SSO do Azure AD para o Roadmunk

Configure e teste o SSO do Azure AD com o Roadmunk usando um usuário de teste chamado *B.Fernandes*. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Roadmunk.

Aqui está uma visão geral de como configurar e testar o SSO do Azure AD com o Roadmunk:

1. [Configure o SSO do Azure AD](#configure-azure-ad-sso) para que seus usuários possam usar esse recurso.
    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o SSO do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o SSO do Azure AD.
1. [Configure o SSO do Roadmunk](#configure-roadmunk-sso) para definir as configurações de SSO do lado do aplicativo.
    1. [Crie um usuário de teste do Roadmunk](#create-roadmunk-test-user) para ter um equivalente de B.Fernandes no Roadmunk que esteja vinculado à representação da usuária no Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. No portal do Azure, na página de integração de aplicativos do **Roadmunk**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela mostrando o ícone Editar para a Configuração Básica do SAML.](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso você tenha um arquivo de metadados de SP e deseje configurar no modo iniciado por IdP, execute estas etapas:

    a. Selecione **Carregar o arquivo de metadados**.

    ![Captura de tela mostrando o link para Carregar arquivo de metadados.](common/upload-metadata.png)

    b. Selecione o ícone de pasta para escolher o arquivo de metadados que você baixou na etapa 4 do procedimento "Configurar o SSO do Roadmunk". Em seguida, selecione **Carregar**.

    ![Captura de tela mostrando como escolher o arquivo de metadados.](common/browse-upload-metadata.png)

    Depois que o arquivo de metadados for carregado, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente na seção **Configuração Básica do SAML**.

    ![Captura de tela que mostra a seção Configuração Básica do SAML. O campo Identificador e o campo URL de Resposta são realçados.](common/idp-intiated.png)

    > [!Note]
    > Se os valores de **Identificador** e **URL de Resposta** não forem populados automaticamente, insira-os manualmente.

1. Caso queira configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais**. No campo **URL de Entrada**, digite `https://login.roadmunk.com`

    ![Captura de tela mostrando onde definir uma URL de logon para o modo iniciado por SP.](common/metadata-upload-additional-signon.png)

1. Na página **Configurar logon único com SAML** na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação**. Depois, selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![Captura de tela mostrando o link de download para o certificado de autenticação SAML.](common/metadataxml.png)

1. Na seção **Configurar o Roadmunk**, copie as URLs necessárias.

    ![Captura de tela mostrando onde copiar as URLs de configuração.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure. Você nomeará o usuário *B.Fernandes*.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da janela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, insira `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o SSO do Azure concedendo-lhe acesso ao Roadmunk.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Roadmunk**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, na lista **Usuários**, selecione **B. Fernandes**. Na parte inferior da caixa de diálogo, escolha **Selecionar**.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a no menu suspenso **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, a função **Acesso Padrão** será selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-roadmunk-sso"></a>Configurar o SSO do Roadmunk

1. Entre no site do Roadmunk como administrador.

1. Na parte inferior da página, selecione o ícone de usuário e, em seguida, selecione **Configurações da Conta**.

    ![Captura de tela mostrando onde selecionar as configurações da conta de usuário.](./media/roadmunk-tutorial/account.png)

1. Acesse **Empresa** > **Configurações de Autenticação**.

1. Na página **Configurações de Autenticação**, execute as seguintes etapas:

    ![Captura de tela mostrando a página Configurações de Autenticação.](./media/roadmunk-tutorial/saml-sso.png)

    a. Ative o **SSO (Logon Único) do SAML**.

    b. Na seção **Etapa 1**, carregue o arquivo XML de metadados ou forneça a URL para os metadados.

    c. Na seção **Etapa 2**, baixe o arquivo de metadados do Roadmunk e salve-o em seu computador.

    d. Se você quiser entrar usando o SSO, na seção **Etapa 3**, selecione **Impor Apenas a Entrada por SAML**.

    e. Clique em **Salvar**.


### <a name="create-roadmunk-test-user"></a>Criar um usuário de teste do Roadmunk

1. Entre no site do Roadmunk como administrador.

1. Na parte inferior da página, selecione primeiro o ícone de usuário e depois **Configurações da Conta**.

    ![Captura de tela mostrando como abrir as Configurações da Conta para o usuário de teste.](./media/roadmunk-tutorial/account.png)

1. Abra a guia **Usuários** e, em seguida, selecione **Convidar Usuário**.

    ![Captura de tela mostrando a guia Usuários. O botão Convidar Usuário está realçado. Na janela aberta, os campos Email e Função são realçados.](./media/roadmunk-tutorial/create-user.png)

1. No formulário que aparece, preencha as informações necessárias e selecione **Convidar**.


## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de SSO do Azure AD usando o painel de acesso.

Ao selecionar o bloco do **Roadmunk** no portal Meus Aplicativos, você será conectado automaticamente à conta do Roadmunk para a qual configurou o SSO. Para obter mais informações, confira [Entrar e iniciar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Roadmunk, você poderá impor o controle de sessão. O controle de sessão fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. 

Saiba como [impor o controle de sessão usando o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
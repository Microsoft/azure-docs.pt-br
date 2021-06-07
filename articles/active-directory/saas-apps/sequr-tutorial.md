---
title: 'Tutorial: Integração do Azure Active Directory ao Genea Access Control | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Genea Access Control.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 82c05f77781abdaea3b2c84aa1071656c206439a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669841"
---
# <a name="tutorial-azure-active-directory-integration-with-genea-access-control"></a>Tutorial: Integração do Azure Active Directory ao Genea Access Control

Neste tutorial, você aprenderá a integrar o Genea Access Control ao Azure AD (Azure Active Directory). Ao integrar o Genea Access Control ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Genea Access Control.
* Permitir que os usuários entrem automaticamente no Genea Access Control com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Genea Access Control, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura do Genea Access Control habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Genea Access Control dá suporte ao SSO iniciado por **SP e IdP**.
> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.


## <a name="adding-genea-access-control-from-the-gallery"></a>Como adicionar o Genea Access Control por meio da galeria

Para configurar a integração do Genea Access Control ao Azure AD, é necessário adicionar o Genea Access Control por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Genea Access Control** na caixa de pesquisa.
1. Selecione **Genea Access Control** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-genea-access-control"></a>Configurar e testar o SSO do Azure AD para o Genea Access Control

Configure e teste o SSO do Azure AD com o Genea Access Control por meio de um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Genea Access Control.

Para configurar e testar o SSO do Azure AD com o Genea Access Control, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Genea Access Control](#configure-genea-access-control-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Genea Access Control](#create-genea-access-control-test-user)** : para ter um equivalente de B.Fernandes no Genea Access Control que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Genea Access Control**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute a seguinte etapa:

    Na caixa de texto **Identificador**, digite a URL: `https://login.sequr.io`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://login.sequr.io`

    b. Na caixa de texto **Estado de retransmissão**, você obterá esse valor, que é explicado posteriormente no tutorial.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Genea Access Control**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Genea Access Control.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Genea Access Control**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.
## <a name="configure-genea-access-control-sso"></a>Configurar o SSO do Genea Access Control

1. Em outra janela do navegador da Web, entre como administrador no seu site de empresa do Genea Access Control.

1. Clique em **Integrações** no painel de navegação esquerdo.

    ![Captura de tela que mostra a opção Integração selecionada no painel de navegação.](./media/sequr-tutorial/configure-1.png)

1. Role para baixo até a seção **Logon Único** e clique em **Gerenciar**.

    ![Captura de tela que mostra a seção Logon Único com o botão Gerenciar selecionado.](./media/sequr-tutorial/configure-2.png)

1. Na seção **Gerenciar Logon Único**, execute as seguintes etapas:

    ![Captura de tela que mostra a seção Gerenciar Logon Único, na qual você pode inserir os valores descritos.](./media/sequr-tutorial/configure-3.png)

    a. Na caixa de texto **URL de logon único de provedor de identidade**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    b. Arraste e solte o arquivo do **Certificado** que você faz o download pelo portal do Azure ou insira manualmente o conteúdo do certificado.

    c. Depois de salvar a configuração, o valor de estado de retransmissão será gerado. Copie o valor de **Estado de Retransmissão** e cole-o na caixa de texto **Estado de Retransmissão** da seção **Configuração Básica de SAML** no portal do Azure.

    d. Clique em **Save** (Salvar).

### <a name="create-genea-access-control-test-user"></a>Criar um usuário de teste do Genea Access Control

Nesta seção, você criará um usuário chamado Brenda Fernandes no Genea Access Control. Trabalhe com a [equipe de suporte ao cliente do Genea Access Control](mailto:support@sequr.io) para adicionar os usuários à plataforma do Genea Access Control. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Genea Access Control, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Genea Access Control e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no Genea Access Control, para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Genea Access Control em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no Genea Access Control, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Genea Access Control, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
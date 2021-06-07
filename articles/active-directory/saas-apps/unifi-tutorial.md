---
title: 'Tutorial: Integração do Azure Active Directory ao UNIFI | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o UNIFI.
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
ms.openlocfilehash: 1fa1c40c96694b71bc762050998257102b687c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650608"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Tutorial: Integração do Azure Active Directory ao UNIFI

Neste tutorial, você aprenderá a integrar o UNIFI ao Azure AD (Azure Active Directory). Ao integrar o UNIFI ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao UNIFI.
* Permitir que os usuários entrem automaticamente no UNIFI com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do UNIFI habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O UNIFI dá suporte ao SSO iniciado por **SP e IdP**.
* O UNIFI dá suporte ao provisionamento de usuário **Automatizado**.

## <a name="add-unifi-from-the-gallery"></a>Adicionar o UNIFI por meio da galeria

Para configurar a integração do UNIFI com o Azure AD, você precisará adicionar o UNIFI à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **UNIFI** na caixa de pesquisa.
1. Selecione **UNIFI** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>Configurar e testar o SSO do Azure AD para o UNIFI

Configure e teste o SSO do Azure AD com o UNIFI usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no UNIFI.

Para configurar e testar o SSO do Azure AD com o UNIFI, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do UNIFI](#configure-unifi-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do UNIFI](#create-unifi-test-user)** : para ter um equivalente de B.Fernandes no UNIFI que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **UNIFI**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    Na caixa de texto **Identificador**, digite a URL `INVIEWlabs`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.discoverunifi.com/login`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o UNIFI**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao UNIFI.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **UNIFI**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-unifi-sso"></a>Configurar o SSO do UNIFI

1. Em outra janela do navegador da Web, entre em seu site de empresa do **UNIFI** como administrador.

2. Clique em **Usuários**.

    ![A captura de tela mostra os Usuários selecionados no site do UNIFI.](./media/unifi-tutorial/app-1.png)

3. Clique em **Adicionar Novo Provedor de Identidade**.

    ![Captura de tela que mostra a opção Adicionar Novo Provedor de Identidade selecionada.](./media/unifi-tutorial/app-2.png)

4. Na seção **Adicionar Provedor de Identidade**, execute as seguintes etapas:

    ![A captura de tela mostra a página Adicionar Provedor de Identidade, na qual você pode inserir os valores descritos.](./media/unifi-tutorial/app-3.png) 

    a. Na caixa de texto **Nome do Provedor**, digite o nome do Provedor de Identidade.

    b. Na caixa de texto **URL do Provedor**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra o certificado que você baixou do Portal do Azure no bloco de notas, remova a marcação **---BEGIN CERTIFICATE---** e **---END CERTIFICATE---** e, em seguida, cole o conteúdo restante na caixa de texto **Certificado**.

    d. Selecione a caixa de seleção **é o Provedor Padrão**.

### <a name="create-unifi-test-user"></a>Criar um usuário de teste do UNIFI

Nesta seção, você criará uma usuária chamada Brenda Fernandes. O **UNIFI** dá suporte ao provisionamento de usuário automático para que não seja necessária nenhuma etapa manual. Os usuários são criados automaticamente após a autenticação bem-sucedida do Azure AD.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do UNIFI, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do UNIFI e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no UNIFI, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do UNIFI em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no UNIFI, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o UNIFI, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
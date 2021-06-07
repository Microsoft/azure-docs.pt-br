---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Timeclock 365 SAML | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Timeclock 365 SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: d0c8364cc85cfce900021272d17456527919122b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99050797"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Timeclock 365 SAML

Neste tutorial, você aprenderá a integrar o Timeclock 365 SAML ao Azure AD (Azure Active Directory). Ao integrar o Timeclock 365 SAML ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao Timeclock 365 SAML.
* Habilitar seus usuários para que entrem automaticamente no Timeclock 365 SAML com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Timeclock 365 SAML habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Timeclock 365 SAML dá suporte ao SSO iniciado por **SP**

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>Adicionando o Timeclock 365 SAML da galeria

Para configurar a integração do Timeclock 365 SAML ao Azure AD, você precisa adicionar o Timeclock 365 SAML da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Timeclock 365 SAML** na caixa de pesquisa.
1. Selecione **Timeclock 365 SAML** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Configurar e testar o SSO do Azure AD para o Timeclock 365 SAML

Configure e teste o SSO do Azure AD com o Timeclock 365 SAML usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Timeclock 365 SAML.

Para configurar e testar o SSO do Azure AD com o Timeclock 365 SAML, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Timeclock 365 SAML](#configure-timeclock-365-saml-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Timeclock 365 SAML](#create-timeclock-365-saml-test-user)** – para ter um equivalente de B. Fernandes no Timeclock 365 SAML que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Timeclock 365 SAML**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite a URL: `https://live.timeclock365.com/login`


1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Timeclock 365 SAML.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Timeclock 365 SAML**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-timeclock-365-saml-sso"></a>Configurar o SSO do Timeclock 365 SAML

1. Para automatizar a configuração no Timeclock 365 SAML, é necessário instalar a **extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Instalar Timeclock 365 SAML**. Você será direcionado ao aplicativo Timeclock 365 SAML. Lá, forneça as credenciais de administrador para entrar no Timeclock 365 SAML. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 4.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Timeclock 365 SAML manualmente, em outra janela do navegador da Web, entre no site da empresa do Timeclock 365 SAML como administrador.

1. Execute as etapas abaixo.

    ![Verificar do Timeclock](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. Vá para a guia **Configurações > Perfil da empresa > Configurações**.

    b. No **caminho de metadados de IDP**, cole a **URL de Metadados de Federação de Aplicativos** copiada do portal do Azure.

    c. Depois, clique em **Atualizar**.

### <a name="create-timeclock-365-saml-test-user"></a>Criar usuário de teste do Timeclock 365 SAML

1. Abra uma nova guia no navegador e entre em seu site de empresa do Timeclock 365 SAML como administrador.

1. Vá para **Usuários > Adicionar novo usuário**.

    ![Criar usuário de teste 1 ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. Forneça todas as informações necessárias na página **Informações do usuário** e clique em **Salvar**.

    ![Criar usuário de teste 2 ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. Clique no botão **Criar** para criar o usuário de teste.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Timeclock 365 SAML, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Timeclock 365 SAML, na qual você iniciará o fluxo de logon.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Timeclock 365 SAML em Meus Aplicativos, isso redirecionará você à URL de logon do Timeclock 365 SAML. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Timeclock 365 SAML, você poderá impor o controle de sessão, que protege contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

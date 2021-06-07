---
title: 'Tutorial: Integração do Azure Active Directory ao Brightspace by Desire2Learn | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Brightspace by Desire2Learn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: 61c4125038ea0c64ff8489a25f9c63c5cbb82a21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583812"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Tutorial: Integração do Active Directory do Azure ao Brightspace by Desire2Learn

Neste tutorial, você aprenderá a integrar o Brightspace by Desire2Learn ao Azure AD (Azure Active Directory). Ao integrar o Brightspace by Desire2Learn ao Azure AD, você pode:

* Controlar no Azure AD quem terá acesso ao Brightspace by Desire2Learn.
* Permitir que os usuários se conectem automaticamente ao Brightspace by Desire2Learn com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único do Brightspace by Desire2Learn.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Brightspace by Desire2Learn dá suporte ao SSO iniciado por **IDP**.

## <a name="add-brightspace-by-desire2learn-from-the-gallery"></a>Adicionar o Brightspace by Desire2Learn por meio da galeria

Para configurar a integração do Brightspace by Desire2Learn ao Azure AD, você precisa adicionar o Brightspace by Desire2Learn à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Brightspace by Desire2Learn** na caixa de pesquisa.
1. Selecione **Brightspace by Desire2Learn** no painel de resultados e, depois, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-brightspace-by-desire2learn"></a>Configurar e testar o SSO do Azure AD para o Brightspace by Desire2Learn

Configure e teste o SSO do Azure AD com o Brightspace by Desire2Learn usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Brightspace by Desire2Learn.

Para configurar e testar o SSO do Azure AD com o Brightspace by Desire2Learn, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Brightspace by Desire2Learn](#configure-brightspace-by-desire2learn-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Brightspace by Desire2Learn](#create-brightspace-by-desire2learn-test-user)** – para ter um equivalente de B.Fernandes no Brightspace by Desire2Learn que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Brightspace by Desire2Learn**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    a. Na caixa de texto **Identificador**, digite a URL usando um dos seguintes padrões:

    ```http
    https://<companyname>.tenants.brightspace.com/samlLogin
    https://<companyname>.desire2learn.com/shibboleth-sp
    ```

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do Brightspace by Desire2Learn](https://www.d2l.com/contact/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Brightspace by Desire2Learn**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Brightspace by Desire2Learn.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Brightspace by Desire2Learn**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-brightspace-by-desire2learn-sso"></a>Configurar o SSO do Brightspace by Desire2Learn

Para configurar o logon único no lado do **Brightspace by Desire2Learn**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Brightspace by Desire2Learn](https://www.d2l.com/contact/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-brightspace-by-desire2learn-test-user"></a>Criar um usuário de teste do Brightspace by Desire2Learn

Nesta seção, você criará um usuário chamado Brenda Fernandes no Brightspace by Desire2Learn. Trabalhe com a [equipe de suporte do Brightspace by Desire2Learn](https://www.d2l.com/contact/) para adicionar os usuários na plataforma do Brightspace by Desire2Learn. Os usuários devem ser criados e ativados antes de usar o logon único.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do Brightspace by Desire2Learn ou as APIs fornecidas pelo Brightspace by Desire2Learn para provisionar as contas de usuário do Active Directory do Azure.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao Brightspace by Desire2Learn para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Brightspace by Desire2Learn em Meus Aplicativos, você deverá ser conectado automaticamente ao Brightspace by Desire2Learn para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Próximas etapas

Após configurar o Brightspace by Desire2Learn, você poderá impor o controle de sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

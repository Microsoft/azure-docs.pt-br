---
title: 'Tutorial: integração do Azure Active Directory ao Cherwell | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cherwell.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: jeedes
ms.openlocfilehash: 5e2cf4ac43fa2a828ebe9e9a5cb5d2e1d630a59a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649361"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Tutorial: Integração do Active Directory do Azure ao Cherwell

Neste tutorial, você aprenderá a integrar o Cherwell ao Azure AD (Azure Active Directory). Ao integrar o Cherwell ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Cherwell.
* Permitir que os usuários sejam conectados automaticamente ao Cherwell com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Cherwell, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).

* Assinatura do Cherwell habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Cherwell dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-cherwell-from-the-gallery"></a>Adicionar o Cherwell por meio da galeria

Para configurar a integração do Cherwell ao Azure AD, você precisa adicionar o Cherwell à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Cherwell** na caixa de pesquisa.
1. Selecione **Cherwell** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-cherwell"></a>Configurar e testar o SSO do Azure AD para o Cherwell

Configure e teste o SSO do Azure AD com o Cherwell usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Cherwell.

Para configurar e testar o SSO do Azure AD com o Cherwell, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    2. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Cherwell](#configure-cherwell-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    1. **[Criar um usuário de teste do Cherwell](#create-cherwell-test-user)** : para ter um equivalente de B.Fernandes no Cherwell que esteja vinculado à representação de usuário do Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Cherwell**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<companyname>.cherwellondemand.com/cherwellclient`

    b. Na caixa de texto **URL de Resposta**, digite a URL usando o seguinte padrão: `https://*.cherwellondemand.com`
    
    > [!NOTE]
    > O valor não é real. Atualize o valor com as URLs de Logon e de Resposta reais. Contate a [equipe de suporte ao Cliente do Cherwell](https://cherwellsupport.com/CherwellPortal) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Cherwell**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B.Fernandes.

1. No painel esquerdo no portal do Azure, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.
1. Na parte superior da tela, selecione **Novo usuário**.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira **B.Fernandes**.  
   1. No campo **Nome de usuário**, insira `<username>@<companydomain>.<extension>`. Por exemplo: `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo o acesso ao Cherwell.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Cherwell**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cherwell-sso"></a>Configurar o SSO do Cherwell

Para configurar o logon único no lado do **Cherwell**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Cherwell](https://cherwellsupport.com/CherwellPortal). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!NOTE]
> A equipe de suporte do Cherwell precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.

### <a name="create-cherwell-test-user"></a>Criar um usuário de teste do Cherwell

Para permitir os usuários do Azure AD entrem no Cherwell, eles deverão ser provisionados no Cherwell. No caso do Cherwell, as contas de usuário precisam ser criadas pela [equipe de suporte do Cherwell](https://cherwellsupport.com/CherwellPortal).

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do Cherwell ou as APIs fornecidas pelo Cherwell para provisionar as contas de usuário do Active Directory do Azure.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Cherwell, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Cherwell e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Cherwell em Meus Aplicativos, você será conectado automaticamente ao Cherwell, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o Cherwell, você poderá impor o Controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
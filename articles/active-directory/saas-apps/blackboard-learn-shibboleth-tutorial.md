---
title: 'Tutorial: integração do Azure Active Directory ao Blackboard Learn - Shibboleth | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Blackboard Learn - Shibboleth.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: b1b3f265d0e1fcad2953292c5227c2630c6df229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649880"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Tutorial: integração do Azure Active Directory ao Blackboard Learn - Shibboleth

Neste tutorial, você aprenderá a integrar o Blackboard Learn – Shibboleth ao Azure AD (Azure Active Directory). Ao integrar o Blackboard Learn – Shibboleth ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Blackboard Learn – Shibboleth.
* Permitir que os usuários sejam conectados automaticamente ao Blackboard Learn – Shibboleth com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:
 
* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Blackboard Learn – Shibboleth habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Blackboard Learn dá suporte ao SSO iniciado por **SP**

## <a name="add-blackboard-learn---shibboleth-from-the-gallery"></a>Adicionar o Blackboard Learn – Shibboleth por meio da galeria

Para configurar a integração do Blackboard Learn - Shibboleth ao Azure AD, você precisará adicionar o Blackboard Learn - Shibboleth da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Blackboard Learn – Shibboleth** na caixa de pesquisa.
1. Selecione **Blackboard Learn – Shibboleth** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-blackboard-learn---shibboleth"></a>Configurar e testar o SSO do Azure AD para o Blackboard Learn – Shibboleth

Configure e teste o SSO do Azure AD com o Blackboard Learn – Shibboleth usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Blackboard Learn – Shibboleth.

Para configurar e testar o SSO do Azure AD com o Blackboard Learn – Shibboleth, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Blackboard Learn – Shibboleth](#configure-blackboard-learn---shibboleth-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Blackboard Learn – Shibboleth](#create-blackboard-learn---shibboleth-test-user)** : para ter um equivalente de B.Fernandes no Blackboard Learn – Shibboleth que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Blackboard Learn – Shibboleth, execute as seguintes etapas:

1. No portal do Azure, na página de integração do aplicativo do **Blackboard Learn – Shibboleth**, selecione **Logon único**.

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

3. Na página **Definir logon único com SAML**, clique no ícone lápis para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao Cliente do Blackboard Learn – Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Blackboard Learn – Shibboleth**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Blackboard Learn – Shibboleth.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Blackboard Learn - Shibboleth**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-blackboard-learn---shibboleth-sso"></a>Configurar o SSO do Blackboard Learn – Shibboleth

Para configurar o logon único no lado do **Blackboard Learn – Shibboleth**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Blackboard Learn – Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-blackboard-learn---shibboleth-test-user"></a>Criar um usuário de teste do Blackboard Learn – Shibboleth

Nesta seção, você deve criar um usuário chamado Brenda Fernandes no Blackboard Learn - Shibboleth. Trabalhe com a [equipe de suporte do Blackboard Learn – Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) para adicionar os usuários na plataforma do Blackboard Learn – Shibboleth. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Blackboard Learn – Shibboleth, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Blackboard Learn – Shibboleth e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Blackboard Learn – Shibboleth em Meus Aplicativos, você será conectado automaticamente ao Blackboard Learn – Shibboleth, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Blackboard Learn – Shibboleth, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
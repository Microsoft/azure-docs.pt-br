---
title: 'Tutorial: integração do Azure Active Directory com o Treinamento de Reconhecimento de Segurança do KnowBe4 | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Azure Active Directory e o Treinamento de Reconhecimento de Segurança do KnowBe4.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: jeedes
ms.openlocfilehash: 77d810fc8327e3777102f2a09f0ca32835aad4b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180754"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Tutorial: integração do Azure Active Directory com o Treinamento de Reconhecimento de Segurança do KnowBe4

Neste tutorial, você aprenderá a integrar o Treinamento de Reconhecimento de Segurança do KnowBe4 ao Azure AD (Azure Active Directory).
A integração do Treinamento de Reconhecimento de Segurança do KnowBe4 ao Azure AD proporciona os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao Treinamento de Reconhecimento de Segurança do KnowBe4.
* Você pode permitir que seus usuários façam logon automaticamente no Treinamento de Reconhecimento de Segurança do KnowBe4 (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Treinamento de Reconhecimento de Segurança do KnowBe4, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Treinamento de Reconhecimento de Segurança do KnowBe4 habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Treinamento de Reconhecimento de Segurança do KnowBe4 é compatível com o SSO iniciado por **SP**

* O Treinamento de Reconhecimento de Segurança do KnowBe4 é compatível com o provisionamento do usuário **Just-In-Time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-knowbe4-from-the-gallery"></a>Adicionando o KnowBe4 da galeria

Para configurar a integração do KnowBe4 ao AD do Azure, você precisará adicionar o KnowBe4 da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **KnowBe4** na caixa de pesquisa.
1. Selecione **KnowBe4** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o KnowBe4 com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do KnowBe4.

Para configurar e testar o logon único do AD do Azure AD com o KnowBe4, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o SSO do Azure AD com a usuária Brenda Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o SSO do Azure AD.
2. **[Configurar o SSO do Treinamento de Reconhecimento de Segurança do KnowBe4](#configure-knowbe4-security-awareness-training-sso)** – para definir as configurações de SSO no lado do aplicativo.
    * **[Criar um usuário de teste do Treinamento de Reconhecimento de Segurança do KnowBe4](#create-knowbe4-security-awareness-training-test-user)** – para ter um equivalente de Brenda Fernandes no Treinamento de Reconhecimento de Segurança do KnowBe4 vinculado à representação do usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **KnowBe4**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > O valor da URL de entrada não é real. Atualize esse valor com a URL de logon real. Entre em contato com a [equipe de suporte do Cliente de Treinamento de Reconhecimento de Segurança do KnowBe4](mailto:support@KnowBe4.com) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Bruto)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

6. Na seção **Configurar o Treinamento de Reconhecimento de Segurança do KnowBe4**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao KnowBe4.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **KnowBe4**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-knowbe4-security-awareness-training-sso"></a>Configurar o SSO de Treinamento de Reconhecimento de Segurança do KnowBe4

Para configurar o logon único no lado do **Treinamento de Reconhecimento de Segurança do KnowBe4**, é necessário enviar o **Certificado (Bruto)** baixado e as URLs corretas copiadas do portal do Azure para a [equipe de suporte do Cliente do Treinamento de Reconhecimento de Segurança do KnowBe4](mailto:support@KnowBe4.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Criar um usuário de teste do Treinamento de Reconhecimento de Segurança do KnowBe4

Nesta seção, você criará uma usuária chamada Brenda Fernandes no KnowBe4. O KnowBe4 é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se nenhum usuário existir no KnowBe4, um será criado após a autenticação.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

1. Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do KnowBe4, na qual você poderá iniciar o fluxo de logon. 

2. Acesse diretamente a URL de Logon do KnowBe4, na qual você iniciará o fluxo de logon.

3. Use o Painel de Acesso da Microsoft. Quando você clicar no bloco do KnowBe4 no Painel de Acesso, será redirecionado para a URL de Logon do KnowBe4. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o KnowBe4, você poderá impor o controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
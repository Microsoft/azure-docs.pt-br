---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao HeyBuddy | Microsoft Docs'
description: Saiba como configurar logon único entre o Azure Active Directory e o HeyBuddy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: c1f102182a9f91d2f8038947b6009fff1af1a508
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914256"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-heybuddy"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao HeyBuddy

Neste tutorial, você aprenderá a integrar o HeyBuddy ao Azure AD (Azure Active Directory). Ao integrar o HeyBuddy ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao HeyBuddy.
* Permitir que os usuários sejam conectados automaticamente ao HeyBuddy com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do HeyBuddy.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O HeyBuddy dá suporte ao SSO iniciado por **SP**
* O HeyBuddy dá suporte ao provisionamento de usuário **Just-In-Time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.


## <a name="adding-heybuddy-from-the-gallery"></a>Adicionando HeyBuddy da galeria

Para configurar a integração do HeyBuddy ao Azure AD, você precisará adicionar o HeyBuddy da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **HeyBuddy** na caixa de pesquisa.
1. Selecione **HeyBuddy** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-heybuddy"></a>Configurar e testar o SSO do Azure AD para o HeyBuddy

Configure e teste o SSO do Azure AD com o HeyBuddy usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no HeyBuddy.

Para configurar e testar o SSO do Azure AD com o HeyBuddy, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do HeyBuddy](#configure-heybuddy-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do HeyBuddy](#create-heybuddy-test-user)** : para ter um equivalente de B.Fernandes no HeyBuddy que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **HeyBuddy**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://api.heybuddy.com/auth/<ENTITY ID>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. O valor `Entity ID` na URL de Entrada é gerado automaticamente para cada organização. Contate a [equipe de suporte do Cliente HeyBuddy](mailto:support@heybuddy.com) para obter esses valores.

1. O aplicativo HeyBuddy espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo EZOfficeInventory espera que mais alguns atributos sejam passados novamente na resposta SAML que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | Nome |  Atributo de Origem|
    | -------- | --------- |
    | Funções  | user.assignedroles |
    | | |
    
    > [!NOTE]
    > Consulte este [link](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) sobre como instalar e configurar as funções para o aplicativo.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao HeyBuddy.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **HeyBuddy**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você tiver configurado as funções conforme explicado acima, será possível selecioná-las na lista suspensa **Selecionar uma função**.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-heybuddy-sso"></a>Configurar o SSO do HeyBuddy

Para configurar o logon único no lado do **HeyBuddy**, é necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do HeyBuddy](mailto:support@heybuddy.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-heybuddy-test-user"></a>Criar um usuário de teste do HeyBuddy

Nesta seção, um usuário chamado Brenda Fernandes será criado no HeyBuddy. O HeyBuddy dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no HeyBuddy, um novo será criado após a autenticação.

> [!Note]
> Se precisar criar um usuário manualmente, contate a [equipe de suporte do HeyBuddy](mailto:support@heybuddy.com).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do HeyBuddy, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do HeyBuddy, na qual você iniciará o fluxo de logon.

* Você pode usar os Meus Aplicativos da Microsoft. Quando clicar no bloco do HeyBuddy em Meus Aplicativos, você será redirecionado para a URL de Logon do HeyBuddy. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o HeyBuddy, você poderá impor o controle de sessão, que protege contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Datava Enterprise Service Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Datava Enterprise Service Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: fa91c634aca5428dcca09c4f23df7c639bf07a39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454823"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-datava-enterprise-service-platform"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Datava Enterprise Service Platform

Neste tutorial, você aprenderá a integrar o Datava Enterprise Service Platform ao Azure AD (Azure Active Directory). Com a integração do Datava Enterprise Service Platform ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Datava Enterprise Service Platform.
* Permitir que os usuários sejam conectados automaticamente ao Datava Enterprise Service Platform com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Datava Enterprise Service Platform habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Datava Enterprise Service Platform dá suporte ao SSO iniciado por **SP**
* O Datava Enterprise Service Platform dá suporte ao provisionamento de usuário **Just-In-Time**
* Após configurar o Datava Enterprise Service Platform, você poderá impor o controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-datava-enterprise-service-platform-from-the-gallery"></a>Adicionando o Datava Enterprise Service Platform por meio da galeria

Para configurar a integração do Datava Enterprise Service Platform ao Azure AD, você precisa adicionar o Datava Enterprise Service Platform à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Datava Enterprise Service Platform** na caixa de pesquisa.
1. Selecione **Datava Enterprise Service Platform** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-datava-enterprise-service-platform"></a>Configurar e testar o SSO do Azure AD para o Datava Enterprise Service Platform

Configure e teste o SSO do Azure AD com o Datava Enterprise Service Platform usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Datava Enterprise Service Platform.

Para configurar e testar o SSO do Azure AD com o Datava Enterprise Service Platform, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Datava Enterprise Service Platform](#configure-datava-enterprise-service-platform-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Datava Enterprise Service Platform](#create-datava-enterprise-service-platform-test-user)** – para ter um equivalente de Brenda Fernandes no Datava Enterprise Service Platform que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Datava Enterprise Service Platform**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://go.datava.com/<TENANT_NAME>`

    b. Na caixa de texto **URL de Resposta**, digite a URL: `https://go.datava.com/saml/module.php/saml/sp/saml2-acs.php/azure-sp`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate [equipe de suporte ao cliente do Datava Enterprise Service Platform](mailto:support@datava.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo acesso ao Datava Enterprise Service Platform.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Datava Enterprise Service Platform**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-datava-enterprise-service-platform-sso"></a>Configurar o SSO do Datava Enterprise Service Platform

Para configurar logon único no lado do **Datava Enterprise Service Platform**, é necessário enviar a **URL de metadados de federação de aplicativos** para a [Equipe de suporte do Datava Enterprise Service Platform](mailto:support@datava.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-datava-enterprise-service-platform-test-user"></a>Criar usuário de teste do Datava Enterprise Service Platform

Nesta seção, um usuário chamado Brenda Fernandes é criado no Datava Enterprise Service Platform. O Datava Enterprise Service Platform é compatível com provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no Datava Enterprise Service Platform, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Datava Enterprise Service Platform no Painel de Acesso, você deverá entrar automaticamente no aplicativo Datava Enterprise Service Platform, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Datava Enterprise Service Platform com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NumlyEngage™ | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NumlyEngage™.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2020
ms.author: jeedes
ms.openlocfilehash: 36f3254a3a309f3dd2612658a82d0dc099ac1991
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92522658"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-numlyengage"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NumlyEngage™

Neste tutorial, você aprenderá a integrar o NumlyEngage™ ao Azure AD (Azure Active Directory). Com a integração do NumlyEngage™ ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao NumlyEngage™.
* Permitir que os usuários, usando as respectivas contas do Azure AD, sejam conectados automaticamente ao NumlyEngage™.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do NumlyEngage™.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O NumlyEngage™ é compatível com SSO iniciado por **SP**
* Depois de configurar o NumlyEngage™, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-numlyengage-from-the-gallery"></a>Como adicionar o NumlyEngage™ da galeria

Para configurar a integração do NumlyEngage™ ao Azure AD, você precisará adicionar o NumlyEngage™ da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **NumlyEngage™** na caixa de pesquisa.
1. Escolha **NumlyEngage™** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-numlyengage"></a>Configurar e testar o SSO do Azure AD para o NumlyEngage™

Configure e teste o SSO do Azure AD com o NumlyEngage™ usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NumlyEngage™.

Para configurar e testar o SSO do Azure AD com o NumlyEngage™, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do NumlyEngage™](#configure-numlyengage-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do NumlyEngage™](#create-numlyengage-test-user)** : para ter um equivalente de B.Fernandes no NumlyEngage™ que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NumlyEngage™**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.numly.io/registration?mail=<CUSTOM_IDENTIFIER>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `urn:amazon:cognito:sp:<NUMLY_ENGAGE_SPECIFIC_IDENTIFIER>`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.NUMLYENGAGE_SPECIFIC_amazoncognito.com/saml2/idpresponse`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, a URL de Resposta e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do NumlyEngage™](mailto:numlyengage-support@numly.io) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo NumlyEngage™ espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo NumlyEngage™ espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem |
    | ------------------ | --------- |
    | Email | user.mail |
    | Número de telefone | user.telephonenumber |

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao NumlyEngage™.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **NumlyEngage™**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-numlyengage-sso"></a>Configurar o SSO do NumlyEngage

Para configurar o logon único no lado do **NumlyEngage™**, é necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do NumlyEngage™](mailto:numlyengage-support@numly.io). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-numlyengage-test-user"></a>Criar um usuário de teste do NumlyEngage

Nesta seção, você criará um usuário com o nome B.Fernandes no NumlyEngage™. Trabalhe com a [equipe de suporte do NumlyEngage™](mailto:numlyengage-support@numly.io) para adicionar os usuários na plataforma do NumlyEngage™. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do NumlyEngage™ no Painel de Acesso, você deverá ser conectado automaticamente ao NumlyEngage™ para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o NumlyEngage™ com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o NumlyEngage™ com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
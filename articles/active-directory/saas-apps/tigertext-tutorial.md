---
title: 'Tutorial: Integração do Azure Active Directory ao TigerConnect Secure Messenger | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TigerConnect Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: jeedes
ms.openlocfilehash: 6831e96ebd8fd2db51d640ff3d93955b1a9f1477
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182148"
---
# <a name="tutorial-azure-active-directory-integration-with-tigerconnect-secure-messenger"></a>Tutorial: Integração do Azure Active Directory ao TigerConnect Secure Messenger

Neste tutorial, você aprenderá a integrar o TigerConnect Secure Messenger ao Azure AD (Azure Active Directory).

A integração do TigerConnect Secure Messenger ao Azure AD oferece os seguintes benefícios:

* Você pode controlar, no Azure AD, quem tem acesso ao TigerConnect Secure Messenger.
* Você pode permitir que os usuários sejam conectados automaticamente ao TigerConnect Secure Messenger (logon único) com as contas do Azure AD deles.
* Gerencie suas contas em um único local: o portal do Azure.

Para obter detalhes sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao TigerConnect Secure Messenger, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do TigerConnect Secure Messenger com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o TigerConnect Secure Messenger ao Azure AD.

* O TigerConnect Secure Messenger dá suporte ao SSO iniciado por **SP**
* Depois de configurar o TigerConnect Secure Messenger, será possível impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-tigerconnect-secure-messenger-from-the-gallery"></a>Adicionar o TigerConnect Secure Messenger por meio da galeria

Para configurar a integração do TigerConnect Secure Messenger ao Azure AD, você precisa adicionar o TigerConnect Secure Messenger da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **TigerConnect Secure Messenger** na caixa de pesquisa.
1. Selecione **TigerConnect Secure Messenger** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o TigerConnect Secure Messenger, com base em um usuário de teste chamado **Brenda Fernandes**. Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TigerConnect Secure Messenger.

Para configurar e testar o logon único do Azure AD com o TigerConnect Secure Messenger, você precisará concluir os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    * **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
    * **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do TigerConnect Secure Messenger](#configure-tigerconnect-secure-messenger-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do TigerConnect Secure Messenger](#create-a-tigerconnect-secure-messenger-test-user)** – para ter o usuário Brenda Fernandes no TigerConnect Secure Messenger vinculado à representação do usuário Brenda Fernandes no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TigerConnect Secure Messenger, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TigerConnect Secure Messenger**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    1. Na caixa **URL de Logon**, insira uma URL:

       `https://home.tigertext.com`

    1. Na caixa **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > O valor **Identificador (ID da entidade)** não é real. Atualize esse valor com o Identificador real. Para obter o valor, entre em contato com a [equipe de suporte do TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). Você também pode consultar os padrões exibidos no painel **Configuração Básica de SAML** no portal do Azure.

1. No painel **Configurar Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para baixar o **XML de Metadados de Federação** das opções fornecidas e salve-o em seu computador.

    ![A opção de download do XML de Metadados de Federação](common/metadataxml.png)

1. Na seção **Configurar o TigerConnect Secure Messenger**, copie as URLs apropriadas com base em suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao TigerConnect Secure Messenger.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TigerConnect Secure Messenger**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-tigerconnect-secure-messenger-sso"></a>Configurar o SSO do TigerConnect Secure Messenger

Para configurar o logon único no lado do TigerConnect Secure Messenger, é necessário enviar o XML de Metadados de Federação baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do TigerConnect Secure Messenger](mailto:prosupport@tigertext.com). A equipe do TigerConnect Secure Messenger verificará se a conexão de SSO do SAML foi definida corretamente em ambos os lados.

## <a name="create-a-tigerconnect-secure-messenger-test-user"></a>Criar um usuário de teste do TigerConnect Secure Messenger

Nesta seção, você criará um usuário chamado Brenda Fernandes no TigerConnect Secure Messenger. Trabalhe com a [equipe de suporte do TigerConnect Secure Messenger](mailto:prosupport@tigertext.com) para adicionar Brenda Fernandes como usuário do TigerConnect Secure Messenger. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Quando escolher **TigerConnect Secure Messenger** no portal Meus Aplicativos, você deverá ser conectado automaticamente à assinatura do TigerConnect Secure Messenger para a qual você configurou o logon único. Para obter mais informações sobre o portal Meus Aplicativos, veja [Acessar e usar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais de integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o Acesso Condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o TigerConnect Secure Messenger com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
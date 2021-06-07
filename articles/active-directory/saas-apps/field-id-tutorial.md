---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Field iD | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Field iD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: 55a66f3f287ffb50d932263e407772efffa839ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453528"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-field-id"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Field iD

Neste tutorial, você aprenderá como integrar o Field iD ao Azure AD (Azure Active Directory). Ao integrar o Field iD ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Field iD.
* Permitir que os usuários sejam conectados automaticamente ao Field iD com as suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Field iD com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Field iD dá suporte ao SSO iniciado por IDP.
* Depois de configurar o Field iD, você poderá impor o controle de sessão. Isso protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-field-id-from-the-gallery"></a>Adicionar o Field iD por meio da galeria

Para configurar a integração do Field iD ao Azure AD, você precisará adicionar o Field iD da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, escolha **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar o novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Field iD** na caixa de pesquisa.
1. Escolha **Field iD** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-field-id"></a>Configurar e testar logon único do Azure AD para o Field iD

Configure e teste o SSO do Azure AD com o Field iD usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Field iD.

Para configurar e testar o SSO do Azure AD com o Field iD, conclua as seguintes etapas:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
    1. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
1. [Configurar o SSO do Field iD](#configure-field-id-sso) para definir as configurações de logon único no lado do aplicativo.
    1. [Criar um usuário de teste do Field iD](#create-a-field-id-test-user) para ter uma contraparte de B.Fernandes no Field iD que esteja vinculada à representação do usuário no Azure AD.
1. [Testar o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Field iD**, localize a seção **Gerenciar**. Em seguida, selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o Logon Único com SAML, com o ícone de lápis realçado](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

   a. Na caixa de texto **Identificador**, digite uma URL que usa o seguinte padrão: `https://<tenantname>.fieldid.com/fieldid`

   b. Na caixa de texto **URL de Resposta**, digite uma URL que use o seguinte padrão: `https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do Field iD](mailto:support@ecompliance.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o ícone de cópia para copiar a **URL de metadados de federação de aplicativos**. Salve-o no computador.

    ![Captura de tela do Certificado de Autenticação SAML, com o ícone de cópia realçado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. Para **Nome**, insira `B.Simon`.  
   1. Para **Nome de usuário**, insira o username@companydomain.extension (por exemplo, `B.Simon@contoso.com`).
   1. Marque a caixa de seleção **Mostrar senha**, em seguida, anote o valor mostrado na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo o acesso ao Field iD.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Field iD**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com Usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Captura de tela de Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários** e clique em **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-field-id-sso"></a>Configurar o SSO do Field iD

Para configurar o logon único no lado do Field iD, envie a **URL de metadados de federação do aplicativo** para a [equipe de suporte do Field iD](mailto:support@ecompliance.com). Eles garantem que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-a-field-id-test-user"></a>Criar um usuário de teste do Field iD

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Field iD. Trabalhe com a [equipe de suporte do Field iD](mailto:support@ecompliance.com) para adicionar os usuários na plataforma do Field iD. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Field iD no Painel de Acesso, você deverá ser conectado automaticamente ao Field iD, para o qual configurou o SSO. Para obter mais informações, confira [Entrar e iniciar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Field iD com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Field iD com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
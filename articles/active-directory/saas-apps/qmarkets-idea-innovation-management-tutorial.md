---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Qmarkets Idea & Innovation Management | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Qmarkets Idea & Innovation Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: jeedes
ms.openlocfilehash: 89be6d631d56e9b3368a351a4f0c521ca327bf3f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522182"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-qmarkets-idea--innovation-management"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Qmarkets Idea & Innovation Management

Neste tutorial, você aprenderá a integrar o Qmarkets Idea & Innovation Management ao Azure AD (Azure Active Directory). Ao integrar o Qmarkets Idea & Innovation Management ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Qmarkets Idea & Innovation Management.
* Permitir que os usuários sejam conectados automaticamente ao Qmarkets Idea & Innovation Management usando as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Qmarkets Idea & Innovation Management.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.



* O Qmarkets Idea & Innovation Management é compatível com SSO iniciado por **SP e IDP**
* O Qmarkets Idea & Innovation Management é compatível com o provisionamento de usuário **Just In Time**


## <a name="adding-qmarkets-idea--innovation-management-from-the-gallery"></a>Como adicionar o Qmarkets Idea & Innovation Management da galeria

Para configurar a integração do Qmarkets Idea & Innovation Management ao Azure AD, adicione o Qmarkets Idea & Innovation Management da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory** .
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos** .
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** .
1. Na seção **Adicionar da galeria** , digite **Qmarkets Idea & Innovation Management** na caixa de pesquisa.
1. Selecione **Qmarkets Idea & Innovation Management** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-qmarkets-idea--innovation-management"></a>Configurar e testar o logon único do Azure AD para Qmarkets Idea & Innovation Management

Configure e teste o SSO do Azure AD com o Qmarkets Idea & Innovation Management usando um usuário de teste chamado **B.Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Qmarkets Idea & Innovation Management.

Para configurar e testar o SSO do Azure AD com o Qmarkets Idea & Innovation Management, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar SSO do Qmarkets Idea & Innovation Management](#configure-qmarkets-idea--innovation-management-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Qmarkets Idea & Innovation Management](#create-qmarkets-idea--innovation-management-test-user)** – para ter um equivalente de B.Fernandes no Qmarkets Idea & Innovation Management vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Qmarkets Idea & Innovation Management** , localize a seção **Gerenciar** e selecione **Logon único** .
1. Na página **Selecionar um método de logon único** , escolha **SAML** .
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML** , caso deseje configurar o aplicativo no modo iniciado por **IDP** , digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<app_url>/sso/saml2/metadata/qmarkets_sp_<endpoint_id>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<app_url>/sso/saml2/acs/qmarkets_sp_<endpoint_id>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon** , digite um URL usando o seguinte padrão: `https://<app_url>/sso/saml2/endpoint/qmarkets_sp_<endpoint_id>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do Qmarkets Idea & Innovation Management](mailto:support@qmarkets.net) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML** , na seção **Certificado de Autenticação SAML** , clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** , **Usuários** e, em seguida, **Todos os usuários** .
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha** .
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure dando a ela acesso ao Qmarkets Idea & Innovation Management.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos** .
1. Na lista de aplicativos, selecione **Qmarkets Idea & Innovation Management** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

## <a name="configure-qmarkets-idea--innovation-management-sso"></a>Configurar o SSO do Qmarkets Idea & Innovation Management

Para configurar o logon único no lado do **Qmarkets Idea & Innovation Management** , envie a **URL de Metadados de Federação de Aplicativo** para a [equipe de suporte do Qmarkets Idea & Innovation Management](mailto:support@qmarkets.net). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-qmarkets-idea--innovation-management-test-user"></a>Criar usuário de teste do Qmarkets Idea & Innovation Management

Nesta seção, um usuário chamado Brenda Fernandes é criado no Qmarkets Idea & Innovation Management. O Qmarkets Idea & Innovation Management é compatível com provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Qmarkets Idea & Innovation Management, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Qmarkets Idea & Innovation Management no Painel de Acesso, você deverá ser conectado automaticamente ao Qmarkets Idea & Innovation Management para o qual você configura o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Avaliar o Qmarkets Idea & Innovation Management com o Azure AD](https://aad.portal.azure.com/)
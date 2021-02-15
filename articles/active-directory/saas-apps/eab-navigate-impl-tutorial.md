---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EAB Navigate IMPL | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EAB Navigate IMPL.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: a3f1982e72fb572f75789f6005b32de557c0b37d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454466"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-impl"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao EAB Navigate IMPL

Neste tutorial, você aprenderá a integrar o EAB Navigate IMPL ao Azure AD (Azure Active Directory). Ao integrar o EAB Navigate IMPL ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao EAB Navigate IMPL.
* Permitir que os usuários sejam conectados automaticamente ao EAB Navigate IMPL com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do EAB Navigate IMPL.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EAB Navigate IMPL dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-eab-navigate-impl-from-the-gallery"></a>Adicionar o EAB Navigate IMPL da galeria

Para configurar a integração do EAB Navigate IMPL ao Azure AD, você precisará adicionar o EAB Navigate IMPL da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria** , digite **EAB Navigate IMPL** na caixa de pesquisa.
1. Selecione **EAB Navigate IMPL** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-impl"></a>Configurar e testar o logon único do Azure AD para o EAB Navigate IMPL

Configure e teste o SSO do Azure AD com o EAB Navigate IMPL usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no EAB Navigate IMPL.

Para configurar e testar o SSO do Azure AD com o EAB Navigate IMPL, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do EAB Navigate IMPL](#configure-eab-navigate-impl-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do EAB Navigate IMPL](#create-eab-navigate-impl-test-user)** – para ter um equivalente de B.Fernandes no EAB Navigate IMPL que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EAB Navigate IMPL** , localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único** , escolha **SAML**.
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML** , insira os valores para os seguintes campos:  Na caixa de texto **Identificador (ID da Entidade)** , insira exatamente o seguinte valor: `https://impl.bouncer.eab.com`
    
    Na caixa de texto **URL de Resposta (URL do Serviço do Consumidor de Declaração)** , insira os seguintes valores como linhas separadas: `https://impl.bouncer.eab.com/sso/saml2/acs`
    `https://impl.bouncer.eab.com/sso/saml2/acs/`
    
    Na caixa de texto **URL de logon** , digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.navigate.impl.eab.com/`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do EAB Navigate IMPL](mailto:EABTechSupport@eab.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML** , na seção **Certificado de Autenticação SAML** , clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** , **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao EAB Navigate IMPL.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **EAB Navigate IMPL**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir**.

## <a name="configure-eab-navigate-impl-sso"></a>Configurar o SSO do EAB Navigate IMPL

Para configurar logon único no lado do **EAB Navigate IMPL** , é necessário enviar a **URL de Metadados de Federação de Aplicativos** para a [equipe de suporte do EAB Navigate IMPL](mailto:EABTechSupport@eab.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-eab-navigate-impl-test-user"></a>Criar usuário de teste do EAB Navigate IMPL

Nesta seção, você criará um usuário chamado B.Fernandes no EAB Navigate IMPL. Trabalhe com [a equipe de suporte do EAB Navigate IMPL](mailto:EABTechSupport@eab.com) para adicionar os usuários na plataforma do EAB Navigate IMPL. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do EAB Navigate IMPL no Painel de Acesso, você deverá ser conectado automaticamente ao EAB Navigate IMPL no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o EAB Navigate IMPL com o Azure AD](https://aad.portal.azure.com/)
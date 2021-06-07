---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Zylo | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zylo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4041dc39-c98a-4d65-bb4f-455bb5d79541
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c84c126e3b611b0bf37abec4f07ab7f3f187b1b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92517388"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zylo"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Zylo

Neste tutorial, você aprenderá a integrar o Zylo ao Azure AD (Azure Active Directory). Ao integrar o Zylo com o Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Zylo.
* Permitir que os usuários sejam conectados automaticamente ao Zylo com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Zylo.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Zylo é compatível com SSO iniciado por **SP e IDP**
* O Zylo é compatível com o provisionamento de usuário **Just-In-Time**
* Depois de configurar o Zylo, você poderá impor um controle de sessão, que fornece proteção contra a exfiltração e a infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-zylo-from-the-gallery"></a>Como adicionar o Zylo usando a galeria

Para configurar a integração do Zylo ao Azure AD, você precisará adicionar o Zylo da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Zylo** na caixa de pesquisa.
1. Selecione **Zylo** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-zylo"></a>Configurar e testar o SSO do Azure AD para o Zylo

Configure e teste o SSO do Azure AD com o Zylo usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zylo.

Para configurar e testar o SSO do Azure AD com o Zylo, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Zylo](#configure-zylo-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zylo](#create-zylo-test-user)** – para ter um equivalente de B.Fernandes no Zylo que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zylo**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://api.zylo.com/saml/sso/azuread/<CUSTOMER_NAME>` 

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.zylo.com/login`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte ao cliente do Zylo](mailto:support@zylo.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Zylo**, copie as URLs apropriadas de acordo com seus requisitos.

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

Nesta seção, você habilitará B.Fernandes a usar o logon único do Azure concedendo a ela acesso ao Zylo.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Zylo**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-zylo-sso"></a>Configurar o SSO do Zylo

1. Faça logon no site do Zylo, como administrador, em uma janela separada.

1. Clique no **Menu** do Zylo no canto superior direito e selecione **Administrador**

    ![Configuração do Zylo](./media/zylo-tutorial/click-admin.png)

1. Na página **Administrador**, vá para a guia **Informações do SAML** e execute as seguintes etapas:

    ![Configuração](./media/zylo-tutorial/saml-configuration-zylo.png)

    a. Altere a **Configuração SAML do Zylo** para **Ativado**.

    b. Selecione o **Provedor de Identidade** como **Azure AD** na lista suspensa.

    c. Na caixa de texto **URL de SSO SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa de texto **Emissor do Provedor de Identidade**, cole o valor **ID da entidade** copiado do portal do Azure.

    e.  Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado Público (do Provedor de Identidade)** .

    f. Clique em **Save**.


### <a name="create-zylo-test-user"></a>Criar usuário de teste do Zylo

Nesta seção, um usuário chamado B.Fernandes será criado no Zylo. O Zylo é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Zylo, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Zylo no Painel de Acesso, você deverá entrar automaticamente no Zylo para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Zylo com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
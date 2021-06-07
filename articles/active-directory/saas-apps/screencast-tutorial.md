---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Screencast-O-Matic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Screencast-O-Matic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.openlocfilehash: 2b0c42046df716c8ae65046e5f3314817da0a17e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92893736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Screencast-O-Matic

Neste tutorial, você aprenderá a integrar o Screencast-O-Matic ao Azure AD (Azure Active Directory). Ao integrar o Screencast-O-Matic ao Azure AD, você poderá:

* Controlar quem tem acesso ao Screencast-O-Matic.
* Permitir que os usuários sejam automaticamente conectados ao Screencast-O-Matic com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Screencast-O-Matic.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Screencast-O-Matic dá suporte ao SSO iniciado por **SP**
* O Screencast-O-Matic é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Adicionando Screencast O Matic da galeria

Para configurar a integração do Screencast-O-Matic ao Azure AD, é necessário adicionar o Screencast-O-Matic à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Screencast-O-Matic** na caixa de pesquisa.
1. Escolha **Screencast-O-Matic** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Configurar e testar o logon único do Azure AD para o Screencast-O-Matic

Configure e teste o SSO do Azure AD com o Screencast-O-Matic por meio de um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Screencast-O-Matic.

Para configurar e testar o SSO do Azure AD com o Screencast-O-Matic, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Screencast-O-Matic](#configure-screencast-o-matic-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Screencast-O-Matic](#create-screencast-o-matic-test-user)** – para ter um equivalente de Brenda Fernandes no Screencast-O-Matic vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Screencast-O-Matic**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [Equipe de suporte do Cliente Screencast Matic O](mailto:support@screencast-o-matic.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Screencast-O-Matic**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nessa seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder a ela o acesso ao Screencast-O-Matic.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Matic O Screencast**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-screencast-o-matic-sso"></a>Configurar o SSO do Screencast-O-Matic

1. Para automatizar a configuração no Screencast-O-Matic, é necessário instalar a **Extensão de navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, um clique em **Configurar o Screencast-O-Matic** direcionará você ao aplicativo Screencast-O-Matic. Nele, forneça as credenciais de administrador para entrar no Screencast-O-Matic. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 11.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o Screencast-O-Matic manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Screencast-O-Matic como administrador e execute as seguintes etapas:

1. Clique em **Assinatura**.

    ![A Assinatura](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. Na seção **Página de acesso**, clique em **Instalar**.

    ![Captura de tela que mostra a seção "Página de Acesso" com o botão "Configurar" selecionado.](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. Na **Página de Acesso da Instalação**, execute as seguintes etapas.

1. Na seção **URL de acesso**, digite o nome de instância na caixa de texto especificada.

    ![Captura de tela que mostra a seção "URL de Acesso" com a caixa de texto do nome da instância realçada.](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Selecione **Exigir Usuário do Domínio** na seção **Restrição de Usuário SAML (opcional)** .

1. Em **Carregar arquivo XML de metadados IDP**, clique em **Escolher Arquivo** para carregar os metadados que você baixou do portal do Azure.

1. Clique em **OK**.

    ![O Acesso](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Criar um usuário de teste do Screencast-O-Matic

Nesta seção, um usuário chamado Brenda Fernandes é criado no Screencast-O-Matic. O Screencast-O-Matic é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se o usuário ainda não existir no Screencast-O-Matic, será criado um novo usuário após a autenticação. Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Screencast-O-Matic](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Screencast-O-Matic no Painel de Acesso, você deve ser conectado automaticamente ao Screencast-O-Matic no qual configurou o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Screencast-O-Matic com o Azure AD](https://aad.portal.azure.com/)
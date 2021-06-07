---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Netskope User Authentication | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Netskope User Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 1ae9e39f5a23725b17451d7144c8304194d2a1d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514360"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Netskope User Authentication

Neste tutorial, você aprenderá a integrar o Netskope User Authentication ao Azure AD (Azure Active Directory). Ao integrar o Netskope User Authentication ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Netskope User Authentication.
* Permitir que os usuários sejam conectados automaticamente ao Netskope User Authentication com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Netskope User Authentication.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Netskope User Authentication dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Adicionar o Netskope User Authentication da galeria

Para configurar a integração do Netskope User Authentication ao Azure AD, você precisa adicionar o Netskope User Authentication da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Netskope User Authentication** na caixa de pesquisa.
1. Selecione **Netskope User Authentication** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Configurar e testar o logon único do Azure AD para o Netskope User Authentication

Configure e teste o SSO do Azure AD com o Netskope User Authentication usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre o usuário do Azure AD e o usuário relacionado no Netskope User Authentication.

Para configurar e testar o SSO do Azure AD com o Netskope User Authentication, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Netskope User Authentication](#configure-netskope-user-authentication-sso)** – para configurar as definições de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Netskope User Authentication](#create-netskope-user-authentication-test-user)** – para ter um equivalente de B.Fernandes no Netskope User Authentication que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Netskope User Authentication**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantname>.goskope.com/<customer entered string>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Você obterá esses valores, o que será explicado posteriormente no tutorial.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Os valores da URL de Logon não é real. Atualize o valor da URL de Logon com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do Netskope User Authentication](mailto:support@netskope.com) para obter o valor da URL de logon. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Netskope User Authentication**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Netskope User Authentication.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Netskope User Authentication**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-netskope-user-authentication-sso"></a>Configurar o SSO do Netskope User Authentication

1. Abra uma nova guia em seu navegador e entre no site da empresa da Autenticação do Usuário do Netskope como administrador.

1. Clique em guia **Plataforma Ativa**.

    ![A captura de tela mostra a Plataforma Ativa selecionada em Configurações.](./media/netskope-user-authentication-tutorial/user1.png)

1. Role para baixo até **ENCAMINHAR PROXY** e selecione **SAML**.

    ![A captura de tela mostra o SAML selecionado em Plataforma Ativa.](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Na página **Configurações de SAML** , execute as seguintes etapas:

    ![A captura de tela mostra Configurações do SAML em que você pode inserir os valores descritos.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Copie o valor da **ID da Entidade SAML** e cole-o na caixa **Identificador** da seção **Configuração Básica de SAML** do portal do Azure.

    b. Copie o valor da **URL do ACS SAML** e cole-o na caixa **URL de Resposta** da seção **Configuração Básica de SAML** do portal do Azure.

1. Clique em **ADICIONAR CONTA**.

    ![A captura de tela mostra a opção ADICIONAR CONTA selecionada no painel do SAML.](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Na página **Adicionar conta SAML**, execute as seguintes etapas:

    ![A captura de tela mostra Adicionar Conta do SAML em que você pode inserir os valores descritos.](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. Na caixa de texto **NOME**, insira o nome como o Azure AD.

    b. Na caixa de texto **URL de IDP**, cole o valor **URL de Logon**, que você copiou do portal do Azure.

    c. Na caixa de texto **ID DA ENTIDADE DO IDP**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    d. Abra seu arquivo de metadados baixado no bloco de notas, copie o conteúdo dele para sua área de transferência e cole-o na caixa de texto **CERTIFICADO DO IDP**.

    e. Clique em **SALVAR**.

### <a name="create-netskope-user-authentication-test-user"></a>Criar um usuário de teste do Netskope User Authentication

1. Abra uma nova guia em seu navegador e entre no site da empresa da Autenticação do Usuário do Netskope como administrador.

1. Clique na guia **Configurações** do painel de navegação esquerdo.

    ![A captura de tela mostra a opção Configuração selecionada.](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Clique em guia **Plataforma Ativa**.

    ![A captura de tela mostra a Plataforma Ativa selecionada em Configurações.](./media/netskope-user-authentication-tutorial/user1.png)

1. Clique na guia **Usuários**.

    ![A captura de tela mostra a opção Usuários selecionada na Plataforma Ativa.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Clique em **ADICIONAR USUÁRIOS**.

    ![A captura de tela mostra a caixa de diálogo Usuários em que você pode selecionar ADICIONAR USUÁRIOS.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Insira o endereço de email do usuário que você deseja adicionar e clique em **ADICIONAR**.

    ![A captura de tela mostra Adicionar Usuários em que você pode inserir uma lista de usuários.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Netskope User Authentication no Painel de Acesso, você deverá ser conectado automaticamente ao Netskope User Authentication, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Netskope User Authentication com o Azure AD](https://aad.portal.azure.com/)
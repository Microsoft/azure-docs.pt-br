---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Segment | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Segment.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: fe8acfd1bfd14f339a0109cab215b8a9ab65256f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021547"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Segment

Neste tutorial, você aprenderá como integrar o Segment ao Azure AD (Azure Active Directory). Quando integrar o Segment ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Segment.
* Permitir que os usuários sejam conectados automaticamente ao Segment com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Segment.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Segment é compatível com SSO iniciado por **SP e IDP**
* O Segment é compatível com provisionamento de usuário **Just-In-Time**

* Depois de configurar o Segment, você poderá impor controle de sessão, que protege contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-segment-from-the-gallery"></a>Adicionar o Segment por meio da galeria

Para configurar a integração do Segment ao Azure AD, você precisa adicionar o Segment por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Segment** na caixa de pesquisa.
1. Escolha **Segment** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-segment"></a>Configurar e testar o logon único do Azure AD para o Segment

Configure e teste o SSO do Azure AD com o Segment usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Segment.

Para configurar e testar o SSO do Azure AD com o Segment, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Segment](#configure-segment-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Segment](#create-segment-test-user)** – para ter um equivalente de B. Fernandes no Segment que esteja vinculado à representação de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Segment**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.segment.com`

    > [!NOTE]
    > Esses valores são espaços reservados. Você precisará usar o identificador, a URL de resposta, a URL de logon reais. As etapas para obter esses valores são descritas mais adiante neste tutorial.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Segment**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Segment.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Segment**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-segment-sso"></a>Configurar o SSO do Segment

1. Em uma janela nova do navegador da Web, entre no site de empresa do Segment como administrador.

1. Clique no **Ícone de Configurações** e role para baixo até **AUTENTICAÇÃO** e clique em **Conexões**.

    ![Captura de tela que mostra o ícone "Configurações" selecionado e a opção "Conexões" selecionada no menu "Autenticação".](./media/segment-tutorial/segment1.PNG)

1. Clique em **Adicionar nova Conexão**.

    ![Uma captura de tela que mostra a seção "Conexões" com o botão "Adicionar nova Conexão" selecionado.](./media/segment-tutorial/segment2.PNG)

1. Selecione **SAML 2.0** como a conexão para configurar e clique em no botão **Selecionar Conexão**.

    ![Captura de tela que mostra a seção "Escolher uma Conexão" com "SAML 2.0" e o botão "Selecionar Conexão" selecionado.](./media/segment-tutorial/segment3.PNG)

1. Na seguinte página, execute as seguintes etapas:

    ![Captura de tela que mostra a página "Configurar Provedor de Identidade" com as caixas de texto "URL de Logon Único" e "URL do Público-alvo" realçadas e o botão "Avançar" selecionado.](./media/segment-tutorial/segment4.PNG)

    a. Copie o valor da **URL de Logon único** e cole-o na caixa **URL de Resposta** na caixa de diálogo **Configuração Básica do SAML** no portal do Azure.

    b. Copie o valor de ****Audience URL**** e cole-o na caixa **URL do Identificador** na caixa de diálogo **Configuração Básica do SAML** no portal do Azure.

    c. Clique em **Avançar**.

    ![Configuração do Segment](./media/segment-tutorial/segment5.PNG)

1. Na caixa **URL de Ponto de Extremidade do SAML 2.0**, cole o valor da **URL de Logon** copiado do portal do Azure.

1. Abra o **Certificado(Base64)** baixado do portal do Azure no Bloco de Notas e cole o conteúdo na caixa de texto **Certificado Público**.

1. Clique em **Configurar Conexão**.

### <a name="create-segment-test-user"></a>Criar usuário de teste do Segment

Nesta seção, um usuário chamado B.Fernandes será criado no Segment. O Segment é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no Segment, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Segment no Painel de Acesso, você deverá ser conectado automaticamente ao Segment, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Segment com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Segment com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
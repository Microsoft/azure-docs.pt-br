---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Amplitude | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Amplitude.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 889fa9b026b741cef438791daf136e0da7cecc24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92318665"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amplitude"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Amplitude

Neste tutorial, você aprenderá a integrar o Amplitude ao Azure AD (Azure Active Directory). Ao integrar o Amplitude ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Amplitude.
* Permitir que os usuários sejam conectados automaticamente ao Amplitude com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Amplitude.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Amplitude é compatível com o SSO iniciado por **SP e IDP**
* O Amplitude é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-amplitude-from-the-gallery"></a>Adicionar o Amplitude da galeria

Para configurar a integração do Amplitude ao Microsoft Azure AD, você precisará adicionar o Amplitude da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Amplitude** na caixa de pesquisa.
1. Selecione **Amplitude** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amplitude"></a>Configurar e testar o logon único do Azure AD para o Amplitude

Configure e teste o SSO do Azure AD com o Amplitude usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Amplitude.

Para configurar e testar o SSO do Azure AD com o Amplitude, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Amplitude](#configure-amplitude-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Amplitude](#create-amplitude-test-user)** – para ter um equivalente de B.Fernandes no Amplitude que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Amplitude**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL: `https://amplitude.com/saml/sso/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://analytics.amplitude.com/saml/sso/<uniqueid>`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Você receberá o valor da URL de resposta posteriormente neste tutorial.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://analytics.amplitude.com/sso`

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Amplitude**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Amplitude.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Amplitude**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-amplitude-sso"></a>Configurar o SSO do Amplitude

1. Para automatizar a configuração no Amplitude, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, um clique em **Configurar o Amplitude** direcionará você ao aplicativo Amplitude. Nele, forneça as credenciais de administrador para entrar no Amplitude. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o Amplitude manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Amplitude como administrador e execute as seguintes etapas:

1. Clique no **Administrador do Plano** da barra de navegação esquerda.

    ![Captura de tela que mostra o menu do Amplitude com Administrador do Plano selecionado.](./media/amplitude-tutorial/configure1.png)

1. Selecione **Metadados do Microsoft Azure Active Directory** da **Integração de SSO**.

    ![Captura de tela que mostra o painel Administrador do Plano com o link para os Metadados do Microsoft Azure Active Directory em destaque.](./media/amplitude-tutorial/configure2.png)

1. Na seção **Configurações de Logon Único**, execute as seguintes etapas:

    ![Captura de tela que mostra a seção Configurar Logon Único com os valores descritos nesta etapa.](./media/amplitude-tutorial/configure3.png)

    a. Abra o **XML de Metadados** baixado do Portal do Azure no bloco de notas, cole o conteúdo na caixa de texto **Metadados do Microsoft Azure Active Directory**.

    b. Copie o valor da **URL de Resposta (ACS)** e cole-o na caixa de texto **URL de Resposta** da **Configuração de SAML Básico** no Portal do Azure.

    c. Clique em **Salvar**

### <a name="create-amplitude-test-user"></a>Criar um usuário de teste do Amplitude

Nesta seção, um usuário chamado B.Fernandes será criado no Amplitude. O Amplitude dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Amplitude, um novo usuário será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Amplitude](https://amplitude.zendesk.com).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Amplitude no Painel de Acesso, você deverá ser conectado automaticamente ao Amplitude, no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Amplitude com o Azure AD](https://aad.portal.azure.com/)
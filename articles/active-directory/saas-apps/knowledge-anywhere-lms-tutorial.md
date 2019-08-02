---
title: 'Tutorial: Integração do Azure Active Directory ao Knowledge Anywhere LMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f44324bbdd5af6675dfb4f5664cbbde2627edfec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098556"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Tutorial: Integração do Knowledge Anywhere LMS ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Knowledge Anywhere LMS ao Azure AD (Azure Active Directory). Quando você integra o Knowledge Anywhere LMS com o Azure AD, você pode:

* Controlar no Azure AD quem terá acesso ao Knowledge Anywhere LMS.
* Permitir que os usuários sejam conectados automaticamente ao Knowledge Anywhere LMS (logon único) com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Knowledge Anywhere LMS.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Knowledge Anywhere LMS dá suporte ao SSO iniciado por **SP** e ao provisionamento de usuário **Just in Time**.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Adicionando o Knowledge Anywhere LMS por meio da galeria

Para configurar a integração do Knowledge Anywhere LMS ao Azure AD, você precisará adicionar o Knowledge Anywhere LMS por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Knowledge Anywhere LMS** na caixa de pesquisa.
1. Escolha **Knowledge Anywhere LMS** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Knowledge Anywhere LMS usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Knowledge Anywhere LMS.

Para configurar e testar o SSO do Azure AD com o Knowledge Anywhere LMS, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar o Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Knowledge Anywhere LMS](#create-knowledge-anywhere-lms-test-user)** para ter um equivalente de B. Fernandes no Knowledge Anywhere LMS que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Knowledge Anywhere LMS**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    1. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais, explicados mais adiante no tutorial.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esse valor com a URL de Entrada real. Contate a [equipe de suporte ao cliente do Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Knowledge Anywhere LMS**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Configurar o Knowledge Anywhere LMS

1. Para automatizar a configuração no Knowledge Anywhere LMS, é necessário instalar a **extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Knowledge Anywhere LMS** e você será direcionado ao aplicativo Knowledge Anywhere LMS. Nele, forneça as credenciais de administrador para entrar no Knowledge Anywhere LMS. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se quiser configurar o Knowledge Anywhere LMS manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Knowledge Anywhere LMS como administrador e execute as seguintes etapas:

4. Selecione a guia **Site**.

    ![Configuração do Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Selecione a guia **Configurações do SAML**.

    ![Configuração do Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Clique em **Adicionar Novo**.

    ![Configuração do Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Na página **Adicionar/Atualizar Configurações do SAML**, execute as seguintes etapas:

    ![Configuração do Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Insira o nome do IdP de acordo com sua organização. Por exemplo: `Azure`.

    b. Na caixa de texto **ID da Entidade do IDP**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL do IDP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Abra o arquivo de certificado baixado do portal do Azure no Bloco de notas, copie o conteúdo do certificado e cole-o na caixa de texto **Certificado**.

    e. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    f. Selecione **Site Principal** na lista suspensa do **Domínio**.

    g. Copie o valor da **ID da Entidade do SP** e cole-o na caixa de texto **Identificador** na seção **Configuração Básica do SAML** no portal do Azure.

    h. Copie o valor da **URL de Resposta do SP (ACS)** e cole-o na caixa de texto **URL de Resposta** na seção **Configuração Básica do SAML** no portal do Azure.

    i. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B. Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela acesso ao Knowledge Anywhere LMS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Knowledge Anywhere LMS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B. Fernandes** na lista Usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Criar um usuário de teste do Knowledge Anywhere LMS

Nesta seção, um usuário chamado B. Fernandes será criado no Knowledge Anywhere LMS. O Knowledge Anywhere LMS dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Knowledge Anywhere LMS, um novo será criado após a autenticação.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Knowledge Anywhere LMS no Painel de Acesso, você deverá ser conectado automaticamente ao Knowledge Anywhere LMS, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
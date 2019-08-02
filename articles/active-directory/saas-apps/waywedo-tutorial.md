---
title: 'Tutorial: Integração do Azure Active Directory com o Way We Do | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Way We Do.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310398"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Tutorial: Integrar o Way We Do com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Way We Do ao Azure AD (Azure AD). Ao integrar o Way We Do ao Azure AD, você poderá:

* Controlar no Azure AD quem terá acesso ao Way We Do.
* Permitir que os usuários sejam conectados automaticamente ao Way We Do com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para logon único (SSO) do Way We Do.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Way We Do dá suporte ao SSO iniciado por **SP**
* O Way We Do dá suporte ao provisionamento de usuário **just-in-time**

## <a name="adding-way-we-do-from-the-gallery"></a>Como adicionar o Way We Do da galeria

Para configurar a integração do Way We Do ao Azure AD, você precisará adicionar o Way We Do da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Way We Do** na caixa de pesquisa.
1. Escolha **Way We Do** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Way We Do usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Way We Do.

Para configurar e testar o SSO do Azure AD com o Way We Do, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Way We Do](#configure-way-we-do-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Way We Do](#create-way-we-do-test-user)** – para ter um equivalente de Brenda Fernandes no Way We Do que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Way We Do**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [Equipe de suporte ao cliente do Way We Do](mailto:support@waywedo.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Raw)** e selecione **Baixar** para salvar o certificado no computador.

   ![O link de download do Certificado](common/certificateraw.png)

1. Na seção **Configurar o Way We Do**, copie as URLs apropriadas com base nas suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Configurar o SSO do Way We Do

1. Para automatizar a configuração no Way We Do é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após adicionar a extensão ao navegador, clicar em **configurar Way We Do** irá direcioná-lo ao aplicativo Way We Do. Em seguida, forneça as credenciais de administrador para entrar no Way We Do. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Se desejar configurar o Way We Do manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Way We Do como administrador e execute as seguintes etapas:

1. Clique no **ícone de pessoa** no canto superior direito de qualquer página do Way We Do e, em seguida, clique em **Conta** no menu suspenso.

    ![Conta do Way We Do](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Clique no **ícone do menu** para abrir o menu de navegação por push e clique em **Logon Único**.

    ![Único do Way We Do](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Na página **Configuração de logon único**, execute as seguintes etapas:

    ![Salvar no Way We Do](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Clique na opção **Ativar logon único** para defini-la como **Sim** para habilitar o Logon Único.

    b. Na caixa de texto **Nome de logon único**, insira seu nome.

    c. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    d. Na caixa de texto **URL do SSO SAML**, cole o valor de **URL de logon**, copiado do portal do Azure.

    e. Carregue o certificado clicando no botão **selecionar** ao lado de **Certificado**.

    f. **Configurações Opcionais** -
    
    * Habilitar senhas – quando essa opção estiver desabilitada, a senha regular funcionará para o Way We Do, de modo que os usuários só poderão usar o logon único.

    * Habilitar o provisionamento automático – quando essa opção está habilitada, o endereço de email usado no logon será comparado automaticamente à lista de usuários no Way We Do. Se o endereço de email não corresponder a um usuário ativo no Way We Do, ele adicionará automaticamente uma nova conta de usuário para a pessoa que está entrando, solicitando quaisquer informações ausentes.

      > [!NOTE]
      > Os usuários adicionados por meio de logon único são adicionados como usuários gerais e não são atribuídos a uma função no sistema. Um Administrador pode entrar e modificar sua função como um editor ou administrador e também pode atribuir uma ou várias funções do Organograma.

    g. Clique em **Salvar** para manter suas configurações.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Way We Do.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Way We Do**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-way-we-do-test-user"></a>Criar um usuário de teste do Way We Do

Nesta seção, um usuário chamado Brenda Fernandes será criado no Way We Do. O Way We Do dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Way We Do, um novo será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao cliente do Way We Do](mailto:support@waywedo.com).

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Way We Do no Painel de Acesso, você deverá ser conectado automaticamente ao Way We Do, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
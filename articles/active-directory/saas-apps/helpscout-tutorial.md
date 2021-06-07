---
title: 'Tutorial: Integração do Azure Active Directory com o Help Scout | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Help Scout.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 7c5e8210bc8b805d72149fd2ef3335c1d637a58f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92445181"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: integração do Azure Active Directory com o Help Scout

Neste tutorial, você aprenderá a integrar o Help Scout ao Azure AD (Azure Active Directory).
A integração do Help Scout ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Help Scout.
* Você pode permitir que os usuários sejam conectados automaticamente ao Help Scout (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao Help Scout, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único do Help Scout

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Help Scout dá suporte ao SSO iniciado por **SP e IDP**
* O Help Scout dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-help-scout-from-the-gallery"></a>Adicionar o Help Scout por meio da galeria

Para configurar a integração do Help Scout com o Azure AD, você precisará adicionar o Help Scout à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Help Scout** na caixa de pesquisa.
1. Selecione **Help Scout** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Help Scout, com base em um usuário de teste chamado **B.Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Help Scout.

Para configurar e testar o logon único do Azure AD com o Help Scout, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Help Scout](#configure-help-scout-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Help Scout](#create-help-scout-test-user)** – para ter um equivalente de B.Fernandes no Help Scout que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Help Scout, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Help Scout**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

1. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Captura de tela que mostra a Configuração Básica de SAML, em que você pode inserir o Identificador e a URL de Resposta e selecionar Salvar.](common/idp-intiated.png)

    a. **Identificador** é o **URI do Público-alvo (ID da Entidade do Provedor de Serviços)** do Help Scout e começa com `urn:`

    b. **URL de Resposta** é a **URL de Postback (URL do Serviço do Consumidor de Declaração)** do Help Scout e começa com `https://` 

    > [!NOTE]
    > Os valores nessas URLs são apenas para demonstração. Atualize esses valores com a URL de Resposta e o Identificador reais. Você obtém esses valores da guia **Logon único** na seção de Autenticação, que é explicada posteriormente no tutorial.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Captura de tela que mostra Definir URLs adicionais, em que você pode inserir uma URL de Logon.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite uma URL como: `https://secure.helpscout.net/members/login/`

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Help Scout**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado B. Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **B.Fernandes**.
  
    b. No campo **Nome de usuário**, digite **B.Fernandes\@dominiodaempresa.extensao**  
    Por exemplo, B.Simon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Help Scout.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Help Scout**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Help Scout**.

    ![O link do Help Scout na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-help-scout-sso"></a>Configurar o SSO do Help Scout

1. Para automatizar a configuração no Help Scout, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, um clique em **Configurar o Help Scout** direcionará você ao aplicativo Help Scout. Nele, forneça as credenciais de administrador para entrar no Help Scout. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

1. Se desejar configurar o Help Scout manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Help Scout como administrador e execute as seguintes etapas:

1. Clique em **Gerenciar** no menu superior e, em seguida, selecione **Empresa** no menu suspenso.

    ![Captura de tela que mostra o menu Gerenciar com Empresa selecionado.](./media/helpscout-tutorial/settings1.png)

1. Selecione **Autenticação** no painel de navegação à esquerda.

    ![Captura de tela que mostra Autenticação selecionado.](./media/helpscout-tutorial/settings2.png)

1. Isso direciona-o à seção de configurações do SAML e executa as seguintes etapas:

    ![Captura de tela mostra a guia Logon Único, em que você insere as informações especificadas.](./media/helpscout-tutorial/settings3.png)

    a. Copie o valor da **URL de Postback (URL do Serviço do Consumidor de Declaração)** e cole o valor na caixa de texto **URL de Resposta** na seção **Configuração Básica do SAML** no portal do Azure.

    b. Copie o valor do **URI do Público-alvo (ID da Entidade do Provedor de Serviços)** e cole-o na caixa de texto **Identificador** na seção **Configuração Básica do SAML** no portal do Azure.

1. Ative a opção **Habilitar SAML** e execute as seguintes etapas:

    ![Captura de tela que mostra a guia Logon Único, em que você habilita o SAML e adiciona outras informações.](./media/helpscout-tutorial/settings4.png)

    a. Na caixa de texto **URL de Logon Único**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Clique em **Carregar Certificado** para carregar o **Certificado(Base64)** baixado no portal do Azure.

    c. Insira os domínios de email de sua organização, por exemplo: `contoso.com` na caixa de texto **Domínios de Email**. Você pode separar múltiplos domínios com uma vírgula. A qualquer momento um Administrador ou Usuário do Help Scout que insere esse domínio específico na [Página de logon do Help Scout](https://secure.helpscout.net/members/login/) serão roteados para o Provedor de Identidade para autenticar com suas credenciais.

    d. Por fim, você pode alternar **Forçar Logon SAML** se desejar que os Usuários façam logon somente no Help Scout por meio desse método. Se você ainda desejar deixar a opção para que eles possam entrar com suas credenciais do Help Scout, pode a deixar desativada. Mesmo se estiver habilitada, um Proprietário de Conta sempre será capaz de fazer logon no Help Scout com a senha da conta.

    e. Clique em **Save** (Salvar).

### <a name="create-help-scout-test-user"></a>Criar um usuário de teste do Help Scout

Nesta seção, um usuário chamado B.Fernandes será criado no Help Scout. O Help Scout dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Help Scout, um novo será criado após a autenticação.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Help Scout no Painel de Acesso, você deverá ser conectado automaticamente ao Help Scout, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Help Scout com o Azure AD](https://aad.portal.azure.com/)
---
title: 'Tutorial: Integração do Azure Active Directory com AlertOps | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b8cb06712e370972e0b8fec518c37d078262e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107066"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Tutorial: Integrar AlertOps com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o AlertOps ao Azure AD (Azure Active Directory). Ao integrar o AlertOps ao Azure AD, você pode:

* Controlar no Azure AD quem terá acesso ao AlertOps.
* Permitir que os usuários sejam conectados automaticamente ao AlertOps com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do AlertOps habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O AlertOps dá suporte ao SSO iniciado por **SP e IDP**.

## <a name="adding-alertops-from-the-gallery"></a>Adicionando AlertOps da galeria

Para configurar a integração do AlertOps ao Azure AD, você precisará adicionar o AlertOps da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da por meio da galeria**, digite **AlertOps** na caixa de pesquisa.
1. Selecione **AlertOps** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o AlertOps usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do AlertOps.

Para configurar e testar o SSO do Azure AD com o AlertOps, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar o AlertOps](#configure-alertops)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do AlertOps](#create-alertops-test-user)** para ter um equivalente de Brenda Fernandes no AlertOps que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **AlertOps**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, selecione **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    1. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.alertops.com`

    1. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte do cliente AlertOps](mailto:support@alertops.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o AlertOps**, copie as URLs apropriadas com base em suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Configurar AlertOps

1. Para automatizar a configuração no AlertOps, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Instalar o AlertOps** direcionará você ao aplicativo AlertOps. Nele, forneça as credenciais de administrador para entrar no AlertOps. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o AlertOps manualmente, abra uma nova janela do navegador da Web, entre no site da empresa AlertOps como administrador e execute as seguintes etapas:

4. Clique em **Configurações de conta** do painel de navegação esquerdo.

    ![Configuração de AlertOps](./media/alertops-tutorial/configure1.png)

5. Na página **Configurações de assinatura**, selecione **SSO** e execute as seguintes etapas:

    ![Configuração de AlertOps](./media/alertops-tutorial/configure2.png)

    a. Marque a caixa de seleção **Usar SSO (Logon Único)** .

    b. Selecione **Azure Active Directory** como um **Provedor de SSO** na lista suspensa.

    c. Na caixa de texto **URL do emissor**, use o identificador de valor que você usou na seção **Configuração de SAML Básica** no portal do Azure.

    d. Na caixa de texto **URL de ponto de extremidade SAML**, cole o valor do **URL de Logon** que você copiou do portal do Azure.

    e. Na caixa de texto **URL de ponto de extremidade SLO**, cole o valor do **URL de Logon** que você copiou do portal do Azure.

    f. Selecione **SHA256** como um **algoritmo de assinatura SAML** na lista suspensa.

    g. Abra o arquivo Certificate(Base64) baixado no Bloco de Notas. Copie o conteúdo dele para a área de transferência e cole-o na caixa de texto Certificado X.509.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao AlertOps.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **AlertOps**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-alertops-test-user"></a>Criar usuário de teste AlertOps

1. Em outra janela do navegador, entre em seu site de empresa do AlertOps como administrador.

2. Clique em **Usuários** do painel de navegação à esquerda.

    ![Configuração de AlertOps](./media/alertops-tutorial/user1.png)

3. Selecione **Adicionar usuário**.

    ![Configuração de AlertOps](./media/alertops-tutorial/user2.png)

4. No diálogo **Adicionar Usuário** , realize as seguintes etapas:

    ![Configuração de AlertOps](./media/alertops-tutorial/user3.png)

    a. Na caixa de texto **Nome de Usuário de Logon**, insira o nome de usuário, como **Brendafernandes**.

    b. Na caixa de texto **Email Oficial**, insira o endereço de email do usuário como **brendafernandes\@contoso.com**.

    c. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    d. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    e. Selecione o valor **tipo** na lista suspensa de acordo com sua organização.

    f. Selecione a **Função** do usuário na lista suspensa de acordo com sua organização.

    g. Selecione **Adicionar**.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do AlertOps no Painel de Acesso, você deverá ser conectado automaticamente ao AlertOps, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
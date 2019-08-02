---
title: 'Tutorial: Integração do Azure Active Directory ao Dropbox for Business | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 591d8d28be5fc9322de8cf4886dc5924f53b1deb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103779"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Tutorial: Integrar o Dropbox for Business ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Dropbox for Business ao Azure AD (Azure Active Directory). Quando integrar o Dropbox for Business ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Dropbox for Business.
* Permitir que os usuários sejam conectados automaticamente ao Dropbox for Business com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para logon único (SSO) do Dropbox for Business.

## <a name="scenario-description"></a>Descrição do cenário

* Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Dropbox para Business dá suporte ao SSO iniciado por **SP**

* O Dropbox for Business dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Adicionando o Dropbox for Business por meio da galeria

Para configurar a integração do Dropbox for Business ao Azure AD, é necessário adicionar o Dropbox for Business à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Dropbox for Business** na caixa de pesquisa.
1. Escolha **Dropbox for Business** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Dropbox for Business usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Dropbox for Business.

Para configurar e testar o SSO do Azure AD com o Dropbox for Business, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Dropbox for Business](#configure-dropbox-for-business-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Dropbox for Business](#create-dropbox-for-business-test-user)** – para ter um equivalente de Brenda Fernandes no Dropbox for Business que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Dropbox for Business**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.dropbox.com/sso/<id>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite um valor: `Dropbox`

    > [!NOTE]
    > O valor da URL de entrada anterior não é um valor real. Você atualizará o valor com a URL de entrada real, que é explicada no tutorial posteriormente.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Dropbox for Business**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-dropbox-for-business-sso"></a>Configurar o SSO do Dropbox for Business

1. Para automatizar a configuração no Dropbox for Business, é necessário instalar a **extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Dropbox for Business** e será direcionado para o aplicativo Dropbox for Business. Forneça as credenciais de administrador para entrar no Dropbox for Business. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar manualmente o Dropbox for Business, abra uma nova janela do navegador da Web, acesse o locatário do Dropbox for Business e entre no locatário do Dropbox for Business. Execute as seguintes etapas:

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/ic769509.png "Configurar logon único")

4. Clique na guia **ícone do usuário** e selecione **configurações**.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure1.png "Configurar logon único")

5. No painel de navegação à esquerda, clique em **console de administração**.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure2.png "Configurar logon único")

6. Sobre o **console de administração**, clique em **configurações** no painel de navegação à esquerda.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure3.png "Configurar logon único")

7. Selecione a opção **Conexão única** na seção **Autenticação**.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure4.png "Configurar logon único")

8. Na seção **Conexão única**, execute as seguintes etapas:  

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure5.png "Configurar logon único")

    a. Selecione **Obrigatório** como uma opção no menu suspenso para o **Logon único**.

    b. Clique em **Adicionar URL de credenciais** e, na caixa de texto **URL de credenciais do provedor de identidade**, cole o valor da **URL de Logon** copiado no portal do Azure e, em seguida, selecione **Concluído**.

    ![Configurar logon único](./media/dropboxforbusiness-tutorial/configure6.png "Configurar logon único")

    c. Clique em **Carregar certificado** e, em seguida, navegue até o **arquivo de certificado codificado em Base64** que você baixou do portal do Azure.

    d. Clique em **Copiar link** e cole o valor copiado na caixa de texto **URL de login** da seção **Dropbox for Business Domain e URLs** no portal do Azure.

    e. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Dropbox for Business.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Dropbox for Business**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-dropbox-for-business-test-user"></a>Criar um usuário de teste do Dropbox for Business

Nesta seção, um usuário chamado Brenda Fernandes é criado no Dropbox for Business. O Dropbox for Business dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Dropbox for Business, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Dropbox for Business](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Testar o SSO

Quando escolher o bloco do Dropbox for Business no Painel de Acesso, você deverá ser conectado automaticamente ao Dropbox for Business para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
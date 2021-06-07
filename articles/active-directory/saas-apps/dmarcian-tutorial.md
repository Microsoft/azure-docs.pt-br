---
title: 'Tutorial: Integração do Azure Active Directory ao dmarcian | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o dmarcian.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 4a5ea47352bfbe86fe194821da0b6bf10818caf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454721"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Tutorial: Integrar o dmarcian ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o dmarcian ao Azure AD (Azure Active Directory). Ao integrar o dmarcian ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao dmarcian.
* Permitir que os usuários entrem automaticamente no dmarcian com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do dmarcian habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O dmarcian é compatível com SSO iniciado por **SP e IDP**

## <a name="adding-dmarcian-from-the-gallery"></a>Como adicionar dmarcian da galeria

Para configurar a integração do dmarcian ao Azure AD, você precisará adicionar o dmarcian da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **dmarcian** na caixa de pesquisa.
1. Selecione **dmarcian** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o dmarcian usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do dmarcian.

Para configurar e testar o SSO do Azure AD com o dmarcian, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do dmarcian](#configure-dmarcian-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste no dmarcian](#create-dmarcian-test-user)** – para ter um equivalente de B.Fernandes no dmarcian vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **dmarcian**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    ```http
    https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml
    ```

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/
    ```

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:
    
    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>
    https://dmarcian-eu.com/login/<ACCOUNT_ID>
    https://dmarciam-ap.com/login/<ACCOUNT_ID>
    ```
     
    > [!NOTE] 
    > Esses valores não são reais. Você atualizará esses valores com o identificador, URL de resposta e URL de logon reais, explicado mais adiante no tutorial.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Configurar o SSO do dmarcian

1. Para automatizar a configuração no dmarcian, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Instalação do dmarcian**. Você será direcionado ao aplicativo dmarcian. Em seguida, forneça as credenciais de administrador para entrar no dmarcian. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o dmarcian manualmente, abra uma nova janela do navegador da Web, entre no site da empresa do dmarcian como administrador e execute as etapas a seguir:

4. Clique em **Perfil** no canto superior direito canto e navegue até **Preferências**.

    ![As preferências](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Role para baixo e clique na seção **Logon Único** e, em seguida, clique em **Configurar**.

    ![Único](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Na página **Logon Único de SAML**, defina o **Status** como **Habilitado** e execute as seguintes etapas:

    ![A de autenticação](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Na seção **Adicionar dmarcian ao seu provedor de identidade**, clique em **Copiar** para copiar a **URL de serviço do consumidor de declaração** para sua instância e colá-lo na caixa de texto **URL de Resposta** na **seção de Configuração SAML Básica** no portal do Azure.

    * Na seção **Adicionar dmarcian ao seu Provedor de Identidade**, clique em **COPY** para copiar a **ID da Entidade** para sua instância e colá-lo na caixa de texto **Identificador** na **seção de Configuração SAML Básica** no portal do Azure.

    * Na seção **Configurar Autenticação**, a caixa de texto **Metadados do Provedor de Identidade** cola a **Url de Metadados de Federação do Aplicativo**, que você copiou do portal do Azure.

    * Na seção **Configurar a Autenticação**, na caixa de texto **Declarações de Atributo**, cole a URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * Na seção **Configurar URL de Logon**, copie a **URL de Logon** para sua instância e cole-a na caixa de texto **URL de Logon** na **seção de Configuração SAML Básica** no portal do Azure.

        > [!Note]
        > Você pode modificar a **URL de Logon** de acordo com a sua organização.

    * Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao dmarcian.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **dmarcian**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-dmarcian-test-user"></a>Criar um usuário de teste do dmarcian

Para permitir que os usuários do Azure AD entrem no dmarcian, eles devem ser provisionados no dmarcian. No dmarcian, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no dmarcian como um Administrador de Segurança.

2. Clique em **Perfil** no canto superior direito e navegue até **Gerenciar Usuários**.

    ![O usuário](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. No lado direito da seção **Usuários de SSO**, clique em **Adicionar Novo Usuário**.

    ![A ação de adicionar usuário](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Na janela pop-up **Adicionar Novo Usuário**, realize as seguintes etapas:

    ![O novo usuário](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Na caixa de texto **Email do novo usuário**, insira o email do usuário como **brendafernandes\@contoso.com**.

    b. Se você quiser conceder direitos de administrador ao usuário, selecione **Tornar o Usuário um Administrador**.

    c. Clique em **Adicionar Usuário**.

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do dmarcian no Painel de Acesso, você deve ser conectado automaticamente ao dmarcian para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
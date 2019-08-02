---
title: 'Tutorial: Integração do Azure Active Directory com OrgChart Now | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b96606b5558e0fbb81733b2f548a89bfb38d5f99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095441"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Tutorial: Integração do Azure Active Directory com OrgChart Now

Neste tutorial, você aprenderá a integrar o OrgChart Now ao Azure AD (Azure Active Directory).
A integração do OrgChart Now ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você poderá controlar quem tem acesso ao OrgChart Now.
* Você pode permitir que os usuários sejam conectados automaticamente ao OrgChart Now (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o OrgChart Now, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do OrgChart Now

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O OrgChart Now dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-orgchart-now-from-the-gallery"></a>Adicionar o OrgChart Now a partir da galeria

Para configurar a integração do OrgChart Now ao AD do Azure, você precisará adicionar o OrgChart Now da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OrgChart Now a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **OrgChart Now**, selecione **OrgChart Now** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![OrgChart Now na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o OrgChart Now, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do OrgChart Now.

Para configurar e testar o logon único do AD do Azure com o OrgChart Now, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do OrgChart Now](#configure-orgchart-now-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do OrgChart Now](#create-orgchart-now-test-user)** – para ter um equivalente de Brenda Fernandes no OrgChart Now vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o OrgChart Now, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **OrgChart Now**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo iniciado por **IDP**:

    ![Informações de logon único em Domínio e URLs do OrgChart Now](common/idp-identifier.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://sso2.orgchartnow.com`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` é o **Identificador do Azure AD** copiado da seção **Configurar o OrgChart Now**, descrita mais adiante no tutorial.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o OrgChart Now**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-orgchart-now-single-sign-on"></a>Configurar o logon único do OrgChart Now

Para configurar o logon único no lado do **OrgChart Now**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do OrgChart Now](mailto:ocnsupport@officeworksoftware.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao OrgChart Now.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **OrgChart Now**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **OrgChart Now**.

    ![O link do OrgChart Now na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-orgchart-now-test-user"></a>Criar um usuário de teste do OrgChart Now

Para permitir que os usuários do Azure AD façam logon no OrgChart Now, eles devem ser provisionados no OrgChart Now. 

1. O OrgChart Now dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Um novo usuário é criado durante uma tentativa de acessar o OrgChart Now, caso ele ainda não exista. O recurso de provisionamento de usuário JIT criará apenas um **usuário somente leitura** quando uma solicitação SSO vier de um IDP reconhecido e o email na asserção SAML não for encontrado na lista de usuários. Para esse recurso de provisionamento automático, é necessário criar um grupo de acesso chamado **Geral** no OrgChart Now. Execute as etapas abaixo para criar um grupo de acesso:

    a. Vá para a opção **Gerenciar Grupos** depois de clicar na **engrenagem** no canto superior direito da interface do usuário.

    ![Grupos OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selecione o ícone **Adicionar** e nomeia o grupo **Geral**, em seguida clique em **OK**. 

    ![Adicionar OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selecione a (s) pasta (s) que você deseja que os usuários gerais ou somente de leitura possam acessar:

    ![Pastas OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Bloqueie** as pastas para que apenas usuários administradores possam modificá-las. Em seguida, pressione **OK**.

    ![Bloquear o OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Para criar usuários **Admin** e **usuários de leitura / gravação**, crie manualmente um usuário para obter acesso ao nível de privilégio por SSO. Para provisionar uma conta de usuário, execute as seguintes etapas:

    a. Faça logon no OrgChart Now como Administrador de Segurança.

    b.  Clique em **Configurações** no canto superior direito e navegue até **Gerenciar Usuários**.

    ![Configurações do OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Clique em **Adicionar** para executar as seguintes etapas:

    ![Gerenciar o OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Na caixa de texto **ID de Usuário**, insira a ID de usuário, como **brendafernandes\@contoso.com**.

    * Na caixa de texto **Endereço de email**, insira o email do usuário como **brendafernandes\@contoso.com**.

    * Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do OrgChart Now no Painel de Acesso, você deverá ser conectado automaticamente ao OrgChart Now, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


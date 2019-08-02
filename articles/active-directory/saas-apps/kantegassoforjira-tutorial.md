---
title: 'Tutorial: Integração do Azure Active Directory ao SSO do Kantega para o JIRA | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do Kantega para o JIRA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e2af4891-e3c8-43b3-bdcb-0500c493e9b4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e97a66a1b21b46649a1206eb522c180e84a0a0d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099164"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Tutorial: Integração do Azure Active Directory ao SSO do Kantega para o JIRA

Neste tutorial, você aprende a integrar o SSO do Kantega para o JIRA ao Azure AD (Azure Active Directory).
A integração do SSO do Kantega para o JIRA ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SSO do Kantega para o JIRA.
* Você pode permitir que os usuários se conectem automaticamente ao SSO do Kantega para o JIRA (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SSO do Kantega para o JIRA, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do SSO do Kantega para o JIRA

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O logon único do Kantega para o JIRA dá suporte ao logon único iniciado por **SP e IDP**

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>Adicionando o SSO do Kantega para o JIRA por meio da galeria

Para configurar a integração do SSO do Kantega para o JIRA ao Azure AD, é necessário adicionar o SSO do Kantega para o JIRA à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o SSO do Kantega para o JIRA por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Logon único do Kantega para o JIRA**, selecione **Logon único do Kantega para o JIRA** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Logon único do Kantega para o JIRA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o SSO do Kantega para o JIRA, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do logon único do Kantega para o JIRA.

Para configurar e testar o logon único do Azure AD com o SSO do Kantega para o JIRA, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Kantega para o JIRA](#configure-kantega-sso-for-jira-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SSO do Kantega para o JIRA](#create-kantega-sso-for-jira-test-user)** – para ter um equivalente de Brenda Fernandes no SSO do Kantega para o JIRA que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o logon único do Kantega para o JIRA, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo de **logon único do Kantega para o JIRA**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único de URLs e domínio de logon único do Kantega para o JIRA](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de URLs e domínio de logon único do Kantega para o JIRA](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Esses valores são recebidos durante a configuração do plug-in do Jira, que é explicada adiante no tutorial.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o logon único do Kantega para o JIRA**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-kantega-sso-for-jira-single-sign-on"></a>Configurar o logon único do SSO do Kantega para o JIRA

1. Em outra janela do navegador da Web, entre no servidor local do JIRA como administrador.

1. Passe o cursor do mouse sobre a engrenagem e clique em **Complementos**.

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon1.png)

1. Na seção da guia Complementos, clique em **Localizar novos complementos**. Pesquise **SSO do Kantega para o JIRA (SAML e Kerberos)** e clique no botão **Instalar** para instalar o novo plug-in do SAML.

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon2.png)

1. A instalação do plug-in é iniciada.

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon3.png)

1. Quando a instalação for concluída. Clique em **fechar**

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon33.png)

1.  Clique em **Gerenciar**.

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon34.png)
    
1. O novo plug-in é listado em **INTEGRAÇÕES**. Clique em **Configurar** para configurar o novo plug-in.

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon35.png)

1. Na seção **SAML**. Selecione **Azure AD (Azure Active Directory)** na lista suspensa **Adicionar provedor de identidade**.

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon4.png)

1. Selecione o nível de assinatura como **Básico**.

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon5.png)       

1. Na seção **Propriedades do aplicativo**, realize as seguintes etapas: 

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon6.png)

    a. Copie o valor da **URI da ID do Aplicativo** e use-o como **o Identificador, a URL de Resposta e a URL de Logon** na seção **Configuração básica do SAML** do portal do Azure.

    b. Clique em **Próximo**.

1. Na seção **Importação de metadados**, realize as seguintes etapas: 

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon7.png)

    a. Selecione **Arquivo de metadados no meu computador** e carregue um arquivo de metadados baixado no portal do Azure.

    b. Clique em **Próximo**.

1. Na seção **Nome e localização de SSO**, realize as seguintes etapas:

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon8.png)

    a. Adicione Nome do Provedor de Identidade à caixa de texto **Nome do provedor de identidade** (por exemplo, Azure AD).

    b. Clique em **Próximo**.

1. Verifique o Certificado de autenticação e clique em **Avançar**.

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon9.png)

1. Na seção **Contas de usuário do JIRA**, realize as seguintes etapas:

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon10.png)

    a. Selecione **Criar usuários no Diretório interno do JIRA, se necessário** e insira o nome apropriado do grupo de usuários (podem ser vários números de grupos separados por vírgula).

    b. Clique em **Próximo**.

1. Clique em **Concluir**.

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon11.png)

1. Na seção **Domínios conhecidos do Azure AD**, realize as seguintes etapas:

    ![Configurar o logon único](./media/kantegassoforjira-tutorial/addon12.png)

    a. Selecione **Domínios conhecidos** no painel esquerdo da página.

    b. Insira o nome de domínio na caixa de texto **Domínios conhecidos**.

    c. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SSO do Kantega para o JIRA.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Logon único do Kantega para o JIRA**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SSO do Kantega para o JIRA**.

    ![O link de logon único do Kantega para o JIRA na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-kantega-sso-for-jira-test-user"></a>Criar um usuário de teste do logon único do Kantega para o JIRA

Para permitir que os usuários do Azure AD se conectem ao JIRA, eles precisam estar provisionados no JIRA. No SSO do Kantega para o JIRA, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no JIRA no servidor local como administrador.

1. Passe o cursor do mouse sobre a engrenagem e clique em **Gerenciamento de usuário**.

    ![Adicionar Funcionário](./media/kantegassoforjira-tutorial/user1.png) 

1. Na seção da guia **Gerenciamento de usuário**, clique em **Criar usuário**.

    ![Adicionar Funcionário](./media/kantegassoforjira-tutorial/user2.png) 

1. Na página da caixa de diálogo **"Criar novo usuário"** , execute as seguintes etapas:

    ![Adicionar Funcionário](./media/kantegassoforjira-tutorial/user3.png) 

    a. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    b. Na caixa de texto **Nome completo**, digite o nome completo do usuário, como Brenda Fernandes.

    c. Na caixa de texto **Nome de usuário**, digite o email do usuário, como Brittasimon@contoso.com.

    d. Na caixa de texto **Senha**, digite a senha do usuário.

    e. Clique em **Criar usuário**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco do logon único do Kantega para o JIRA no Painel de Acesso, você deverá ser conectado automaticamente ao logon único do Kantega para o JIRA para o qual você configurar o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

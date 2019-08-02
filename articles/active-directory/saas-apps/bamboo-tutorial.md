---
title: 'Tutorial: Integração do Azure Active Directory com o SSO do SAML para Bamboo da Resolution GmbH | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e SSO do SAML para Bamboo da Resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044a2f9a9ecd0ccceb99ce7999af7e2c8578950d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106527"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Tutorial: Integração do Azure Active Directory com o SSO do SAML para Bamboo da Resolution GmbH

Neste tutorial, você aprenderá a integrar o SSO do SAML para Bamboo da Resolution GmbH com o Azure AD (Azure Active Directory).
A integração de SSO do SAML para Bamboo da Resolution GmbH com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao SSO do SAML para Bamboo da Resolution GmbH.
* Você pode permitir que os usuários sejam conectados automaticamente ao SSO do SAML para Bamboo da Resolution GmbH (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SSO de SAML para Bamboo da Resolution GmbH, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do SSO do SAML para Bamboo da Resolution GmbH

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SSO do SAML para Bamboo da resolution GmbH dá suporte ao SSO iniciado por **SP e IDP**
* O SSO do SAML para Bamboo da resolution GmbH dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Adicionar o SSO do SAML para Bamboo da Resolution GmbH da galeria

Para configurar a integração do SSO do SAML para Bamboo da Resolution GmbH no Azure AD, adicione o SSO do SAML para Bamboo da Resolution GmbH da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar SSO do SAML para Bamboo da Resolution GmbH da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SSO do SAML para Bamboo da Resolution GmbH**, selecione **SSO do SAML para Bamboo da Resolution GmbH** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![SSO do SAML para Bamboo da Resolution GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o SSO de SAML para Bamboo da Resolution GmbH com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO do SAML para Bamboo da Resolution GmbH.

Para configurar e testar o logon único do Azure AD com o SSO de SAML para Bamboo da Resolution GmbH, conclua os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do SSO do SAML para Bamboo da Resolution GmbH](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste de SSO do SAML para Bamboo da Resolution GmbH](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** – para ter um equivalente de Brenda Fernandes no SSO do SAML para Bamboo da Resolution GmbH vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SSO de SAML para Bamboo da Resolution GmbH, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **SSO do SAML para Bamboo da Resolution GmbH**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do SSO do SAML para Bamboo da Resolution GmbH](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de domínio e URLs do SSO do SAML para Bamboo da Resolution GmbH](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao Cliente do SSO do SAML para Bamboo da Resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o SSO do SAML para Bamboo da Resolution GmbH**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Configurar o Logon Único do SSO do SAML para Bamboo da Resolution GmbH

1. Faça logon no site da empresa do SAML SSO para Bamboo da Resolution GmbH como administrador.

1. No lado direito da barra de ferramentas principal, clique em **configurações** > **complementos**.

    ![As configurações](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Vá para a seção SEGURANÇA, clique em **logon único do SAML** na barra de menus.

    ![O Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Na **página de configuração do plug-in de logon único do SAML**, clique em **Adicionar idp**.

    ![Adicionar idp](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Na página **Escolher seu Provedor de Identidade SAML**, execute as seguintes etapas:

    ![O provedor de identidade](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Selecione **Tipo de Idp** como **AD do AZURE**.

    b. Na caixa de texto **Nome**, digite um nome.

    c. Na caixa de texto **Descrição**, digite a descrição.

    d. Clique em **Próximo**.

1. Na página **Configuração do provedor de identidade**, clique no botão **Avançar**.

    ![As configurações de identidade](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Na página **importar metadados de Idp do SAML**, clique em **carregar arquivo** para carregar o arquivo **METADATA XML** que você fez o download do portal do Azure.

    ![O idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Clique em **Próximo**.

1. Clique em **Salvar alterações**.

    ![Salvar](./media/bamboo-tutorial/tutorial_bamboo_save.png)

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

Nesta seção, habilite Britta Simon a usar o logon único do Azure concedendo acesso ao SSO do SAML para Bamboo da Resolution GmbH.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SSO do SAML para Bamboo da Resolution GmbH**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SSO do SAML para Bamboo da Resolution GmbH**.

    ![O SSO do SAML para Bamboo da Resolution GmbH, link na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Criar um usuário de teste do SSO do SAML para Bamboo da Resolution GmbH

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no SSO do SAML para Bamboo da Resolution GmbH. O SSO do SAML para Bamboo da Resolution GmbH dá suporte ao provisionamento just-in-time e os usuários podem ser criados manualmente, entre em contato com a [equipe de suporte ao cliente do SSO do SAML para Bamboo da Resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support), de acordo com as suas necessidades.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SSO do SAML para Bamboo da Resolution GmbH no Painel de Acesso, você deverá ser conectado automaticamente ao SSO do SAML para Bamboo da Resolution GmbH para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

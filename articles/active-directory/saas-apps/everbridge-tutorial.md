---
title: 'Tutorial: Integração do Azure Active Directory ao Everbridge | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Everbridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103248"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Integração do Azure Active Directory ao Everbridge

Neste tutorial, você aprenderá a integrar o Everbridge ao Azure AD (Azure Active Directory).
Ao integrar o Everbridge ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Everbridge.
* Permitir que seus usuários entrem automaticamente no Everbridge usando suas contas do Azure AD. Esse controle de acesso é chamado de logon único (SSO).
* Gerenciar suas contas em um local central usando o portal do Azure.
Para obter mais informações sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Everbridge, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Everbridge que usa logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Everbridge dá suporte ao SSO iniciado por IDP.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Adicionar o Everbridge do Azure Marketplace

Para configurar a integração do Everbridge ao Azure AD, adicione o Everbridge do Azure Marketplace à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Everbridge do Azure Marketplace, siga estas etapas.

1. No [Portal do Azure Microsoft](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![Botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Everbridge**. Selecione **Everbridge** no painel de resultados e selecione **Adicionar**.

     ![Everbridge na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Everbridge com base em um usuário de teste Brenda Fernandes.
Para que o logon único funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Everbridge.

Para configurar e testar o logon único do Azure AD com o Everbridge, conclua os seguintes blocos de construção:

- [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
- [Configurar o Everbridge como o logon único do portal do gerenciador Everbridge](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
- [Configurar o Everbridge como o logon único do portal do membro Everbridge](#configure-everbridge-as-everbridge-member-portal-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
- [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
- [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
- [Criar um usuário de teste do Everbridge](#create-an-everbridge-test-user) para ter um equivalente de Brenda Fernandes no Everbridge que esteja vinculado à representação desse usuário no Azure AD.
- [Testar o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Everbridge, siga estas etapas.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Everbridge**, clique em **Logon Único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com o SAML**, selecione **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

    >[!NOTE]
    >Configure o aplicativo como o portal do gerente *ou* como o portal do membro no portal do Azure e no portal do Everbridge.

4. Para configurar o aplicativo **Everbridge** como o **Portal do gerente do Everbridge**, na seção **Configuração Básica do SAML** execute as seguintes etapas:

    ![Informações de logon único do domínio e URLs do Everbridge](common/idp-intiated.png)

    a. Na caixa **Identificador**, insira uma URL que siga o padrão `https://sso.everbridge.net/<API_Name>`

    b. Na caixa **URL de Resposta**, insira uma URL que siga o padrão `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com os valores reais de Identificador e URL de Resposta. Para obter esses valores, entre em contato com a equipe de suporte do [Everbridge](mailto:support@everbridge.com). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Para configurar o aplicativo **Everbridge** como o **Portal de membro do Everbridge**, na seção **Configuração Básica do SAML**, siga as etapas:

  * Se você quiser configurar o aplicativo no modo iniciado pelo IDP, siga estas etapas:

     ![Domínio Everbridge e informações de logon único de URLs para o modo iniciado pelo IDP](common/idp-intiated.png)

    a. Na caixa **Identificador**, insira uma URL que siga o padrão `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Na caixa **URL de Resposta**, insira uma URL que siga o padrão `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Caso queira configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais** e siga esta etapa:

     ![Domínio Everbridge e informações de logon único de URLs para o modo iniciado pelo SP](common/both-signonurl.png)

     a. Na caixa **URL de Entrada**, insira uma URL que siga o padrão `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Esses valores não são reais. Atualize esses valores com os valores do Identificador, da URL de Resposta e da URL de Logon reais. Para obter esses valores, entre em contato com a equipe de suporte do [Everbridge](mailto:support@everbridge.com). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, selecione **Download** para baixar o **XML de metadados de Federação**. Salve-o no computador.

    ![Link de download do certificado](common/metadataxml.png)

7. Na seção **Configurar o Everbridge**, copie as URLs necessárias para seus requisitos:

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    - URL de logon
    - Identificador do Azure AD
    - URL de logoff

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Configurar o Everbridge como logon único do portal do gerente Everbridge

Para configurar o SSO no **Everbridge** como um aplicativo do **portal do gerente Everbridge**, siga estas etapas.
 
1. Em uma janela do navegador da Web diferente, entre no Everbridge como administrador.

1. No menu na parte superior, selecione a guia **Configurações**. Em **Segurança**, selecione **Logon Único**.
   
     ![Configurar o logon único](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Na caixa **Nome**, insira o nome do provedor do identificador. Um exemplo é o nome da sua empresa.
   
     b. Na caixa **Nome da API**, insira o nome da API.
   
     c. Selecione **Escolher Arquivo** para carregar o arquivo de metadados que você baixou do portal do Azure.
   
     d. Para **Local de Identidade do SAML**, selecione **A identidade está no elemento NameIdentifier da declaração Subject**.
   
     e. Na caixa **URL de Logon do Provedor de Identidade**, cole a **URL de Logon** que você copiou do portal do Azure.
   
     f. Para **Associação de Solicitação Iniciada do Provedor de Serviço**, selecione **Redirecionamento HTTP**.

     g. Clique em **Salvar**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Configurar o Everbridge como logon único do portal do membro Everbridge

Para configurar o logon único no **Everbridge** como um **Portal de membro do Everbridge**, envie o **XML de Metadados de Federação** baixado para a [equipe de suporte do Everbridge](mailto:support@everbridge.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Para criar o usuário de teste Brenda Fernandes no portal do Azure, siga estas etapas.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

    ![Links Usuários e Todos os Usuários](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, siga estas etapas.

    ![Caixa de diálogo Usuário](common/user-properties.png)

    a. Na caixa **Nome**, insira **BrendaFernandes**.
  
    b. Na caixa **Nome de Usuário**, insira `brittasimon@yourcompanydomain.extension`. Um exemplo é BrittaSimon@contoso.com.

    c. Selecione a caixa de seleção **Mostrar Senha**. Anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Permita que Brenda Fernandes use o logon único do Azure concedendo acesso ao Everbridge.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos** >**Everbridge**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Everbridge**.

    ![Link Everbridge na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![Link Usuários e Grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Caixa de diálogo Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários. Escolha **Selecionar** na parte inferior da tela.

6. Se você esperar qualquer valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Escolha **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-an-everbridge-test-user"></a>Criar um usuário de teste do Everbridge

Nesta seção, você cria o usuário de teste Brenda Fernandes no Everbridge. Para adicionar usuários na plataforma Everbridge, trabalhe com a equipe de suporte do [Everbridge](mailto:support@everbridge.com). Os usuários devem ser criados e ativados no Everbridge para você poder usar o logon único. 

### <a name="test-single-sign-on"></a>Testar logon único 

Teste sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar a peça do Everbridge no Painel de Acesso, você deverá ser conectado automaticamente à conta do Everbridge, para a qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Tutorial: Integração do Azure Active Directory ao Proxyclick | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o logon único entre o Azure Active Directory e o Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093503"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Tutorial: Integração do Azure Active Directory ao Proxyclick

Neste tutorial, você aprenderá a integrar o Proxyclick ao Azure AD (Azure Active Directory).
Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao Proxyclick.
* Você pode permitir que os usuários sejam conectados automaticamente ao Proxyclick (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Proxyclick, você precisará ter:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, inscreva-se em uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do Proxyclick que tenha o logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Proxyclick é compatível com o SSO iniciado por SP e IdP.

## <a name="add-proxyclick-from-the-gallery"></a>Adicionar o Proxyclick por meio da galeria

Para configurar a integração do Proxyclick ao Azure AD, é necessário adicionar o Proxyclick por meio da galeria à lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**:

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Proxyclick**. Selecione **Proxyclick** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Proxyclick usando um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no Proxyclick.

Para configurar e testar o logon único do Azure AD com o Proxyclick, você precisará concluir estas etapas:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do Proxyclick](#configure-proxyclick-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Criar um usuário de teste do Proxyclick](#create-a-proxyclick-test-user)** que esteja vinculado à representação do usuário do Azure AD.
6. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Proxyclick, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do Proxyclick, selecione **Logon único**:

    ![Selecionar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por IdP, execute as etapas a seguir.

    ![Caixa de diálogo Configuração Básica de SAML](common/idp-intiated.png)

    1. Na caixa **Identificador**, digite uma URL neste padrão:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Na caixa **URL de Resposta**, digite uma URL neste padrão:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Caso queira configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais**. Na caixa **URL de Logon**, insira uma URL neste padrão:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Informações de logon único em Domínio e URLs do Proxyclick](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Esses valores são espaços reservados. Você precisará usar o identificador, a URL de resposta, a URL de logon reais. As etapas para obter esses valores são descritas mais adiante neste tutorial.

6. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado do **Certificado (Base64)** , de acordo com suas necessidades e salve o certificado no computador:

    ![Link de download do certificado](common/certificatebase64.png)

7. Na seção **Configurar o Proxyclick**, copie as URLs apropriadas de acordo com suas necessidades:

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    1. **Identificador do Azure AD**.

    1. **URL de Logoff**.

### <a name="configure-proxyclick-single-sign-on"></a>Configurar o logon único do Proxyclick

1. Em uma nova janela do navegador da Web, entre em seu site de empresa do Proxyclick como administrador.

2. Selecione **Conta e Configurações**:

    ![Selecionar Conta e Configurações](./media/proxyclick-tutorial/configure1.png)

3. Role a página para baixo até a seção **Integrações** e selecione **SAML**:

    ![Selecionar SAML](./media/proxyclick-tutorial/configure2.png)

4. Na seção **SAML**, execute as etapas a seguir.

    ![Seção SAML](./media/proxyclick-tutorial/configure3.png)

    1. Copie o valor da **URL do Consumidor do SAML** e cole-o na caixa **URL de Resposta** na caixa de diálogo **Configuração Básica do SAML** no portal do Azure.

    1. Copie o valor da **URL de Redirecionamento de SSO do SAML** e cole-o nas caixas **URL de Entrada** e **Identificador** na caixa de diálogo **Configuração Básica do SAML** no portal do Azure.

    1. Na lista **Método de Solicitação do SAML**, selecione **Redirecionamento HTTP**.

    1. Na caixa **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    1. Na caixa **URL de Ponto de Extremidade do SAML 2.0**, cole o valor da **URL de Logon** copiado do portal do Azure.

    1. No Bloco de notas, abra o arquivo de certificado baixado no portal do Azure. Cole o conteúdo desse arquivo na caixa **Certificado**.

    1. Selecione **Salvar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste chamado Brenda Fernandes no portal do Azure.

1. No portal do Azure, selecione **Azure Active Directory** no painel esquerdo, **Usuários** e, em seguida, **Todos os usuários**:

    ![Selecionar Todos os usuários](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela:

    ![Selecionar Novo usuário](common/new-user.png)

3. Na caixa de diálogo **Usuário**, execute as etapas a seguir.

    ![Caixa de diálogo Usuário](common/user-properties.png)

    1. Na caixa **Nome**, insira **BrendaFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **BrendaFernandes@\<domíniodaempresa>.\<extensão>** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **Mostrar Senha** e, em seguida, anote o valor mostrado na caixa **Senha**.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Proxyclick.

1. No portal do Azure, selecione **Aplicativos empresariais**, **Todos os aplicativos** e, em seguida, **Proxyclick**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Proxyclick**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da janela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da janela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-a-proxyclick-test-user"></a>Criar um usuário de testes do Proxyclick

Para permitir que os usuários do Azure AD entrem no Proxyclick, você precisará adicioná-los ao Proxyclick. Você precisará adicioná-los manualmente.

Para criar uma conta de usuário, siga estas etapas:

1. Entre em seu site de empresa do Proxyclick como administrador.

1. Selecione **Colegas** na parte superior da janela:

    ![Selecionar Colegas](./media/proxyclick-tutorial/user1.png)

1. Selecione **Adicionar Colega**:

    ![Selecionar Adicionar Colega](./media/proxyclick-tutorial/user2.png)

1. Na seção **Adicionar um colega**, execute as etapas a seguir.

    ![Adicionar uma seção de colega](./media/proxyclick-tutorial/user3.png)

    1. Na caixa **Email**, insira o endereço de email do usuário. Nesse caso, **brendafernandes\@contoso.com**.

    1. Na caixa **Nome**, insira o nome do usuário. Nesse caso, **Brenda**.

    1. Na caixa **Sobrenome**, insira o sobrenome do usuário. Nesse caso, use **Fernandes**.

    1. Selecione **Adicionar usuário**.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Proxyclick no Painel de Acesso, você deverá ser conectado automaticamente à instância do Proxyclick, para a qual você configurou o SSO. Para obter mais informações sobre o Painel de Acesso, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


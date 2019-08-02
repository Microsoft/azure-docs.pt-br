---
title: 'Tutorial: Integração do Azure Active Directory ao Promapp | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o logon único entre o Azure Active Directory e o Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 2ddb8777a6470c0e739545e71867a694022d1723
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093595"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Tutorial: Integração do Active Directory do Azure com o Promapp

Neste tutorial, você aprenderá a integrar o Promapp ao Azure AD (Azure Active Directory).
Essa integração oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao Promapp.
* Você pode permitir que os usuários sejam conectados automaticamente ao Promapp (logon único) com suas contas do Azure AD.
* Gerencie suas contas em um único local: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Promapp, você precisará ter:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, inscreva-se em uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do Promapp que tenha o logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Promapp é compatível com o SSO iniciado por SP e IdP.

* O Promapp é compatível com o provisionamento de usuário just-in-time.

## <a name="add-promapp-from-the-gallery"></a>Adicionar o Promapp da galeria

Para configurar a integração do Promapp ao Azure AD, você precisará adicionar o Promapp por meio da galeria à lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Acesse **Aplicativos empresariais** > **Todos os aplicativos**:

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **Novo aplicativo** na parte superior da janela:

    ![Selecionar Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Promapp**. Selecione **Promapp** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados da Pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Promapp usando um usuário de teste chamado Brenda Fernandes.
Para habilitar o logon único, você precisará estabelecer uma relação entre um usuário do Azure AD e o usuário correspondente no Promapp.

Para configurar e testar o logon único do Azure AD com o Promapp, você precisará concluir estas etapas:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** para habilitar o recurso para os usuários.
2. **[Configurar o logon único do Promapp](#configure-promapp-single-sign-on)** no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para habilitar o logon único do Azure AD para o usuário.
5. **[Testar o logon único](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Promapp, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do Promapp, selecione **Logon único**:

    ![Selecionar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único:

    ![Selecionar um método de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, selecione o ícone **Editar** para abrir a caixa de diálogo **Configuração Básica de SAML**:

    ![Ícone Editar](common/edit-urls.png)

4. Na caixa de diálogo **Configuração Básica de SAML**, caso deseje configurar o aplicativo no modo iniciado por IdP, realize as seguintes etapas.

    ![Caixa de diálogo Configuração Básica de SAML](common/idp-intiated.png)

    1. Na caixa **Identificador**, digite uma URL neste padrão:

       | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > A integração do Azure AD ao Promapp está configurada atualmente somente para autenticação iniciada pelo serviço. (Ou seja, o acesso de uma URL do Promapp inicia o processo de autenticação). No entanto, o campo **URL de Resposta** é um campo obrigatório.

    1. Na caixa **URL de Resposta**, digite uma URL neste padrão:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

5. Caso queira configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais**. Na caixa **URL de Logon**, insira uma URL neste padrão:

      `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    ![Informações de logon único de Domínio e URLs do Promapp](common/metadata-upload-additional-signon.png)

   

    > [!NOTE]
    > Esses valores são espaços reservados. Você precisará usar o identificador, a URL de resposta, a URL de logon reais. Contate a [equipe de suporte do Promapp](https://www.promapp.com/about-us/contact-us/) para obter os valores. Você também pode ver os padrões exibidos na caixa de diálogo **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Baixar** ao lado do **Certificado (Base64)** , de acordo com suas necessidades e salve o certificado no computador:

    ![Link de download do certificado](common/certificatebase64.png)

7. Na seção **Configurar o Promapp**, copie as URLs apropriadas de acordo com suas necessidades:

    ![Copiar as URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de Logon**.

    1. **Identificador do Azure AD**.

    1. **URL de Logoff**.

### <a name="configure-promapp-single-sign-on"></a>Configurar o logon único do Promapp

1. Entre em seu site de empresa do Promapp como administrador.

2. No menu na parte superior da janela, selecione **Administrador**:
   
    ![Selecionar Administrador][12]

3. Selecione **Configurar**:
   
    ![Selecionar Configurar][13]

4. Na caixa de diálogo **Segurança**, execute as etapas a seguir.
   
    ![Caixa de diálogo Segurança][14]
    
    1. Cole a **URL de logon** copiada do portal do Azure na caixa **URL de Logon SSO**.
    
    1. Na lista **SSO – Modo de Logon Único**, selecione **Opcional**. Clique em **Salvar**.

       > [!NOTE]
       > O modo opcional é destinado somente a teste. Depois que você estiver satisfeito com a configuração, selecione **Obrigatório** na lista **SSO – Modo de Logon Único** para forçar todos os usuários a se autenticarem no Azure AD.

    1. No Bloco de notas, abra o certificado baixado na seção anterior. Copie o conteúdo do certificado sem a primeira linha ( **-----BEGIN CERTIFICATE-----** ) ou a última linha ( **-----END CERTIFICATE-----** ). Cole o conteúdo do certificado na caixa **Certificado x.509 de SSO** e, em seguida, selecione **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Promapp.

1. No portal do Azure, selecione **Aplicativos empresariais**, selecione **Todos os aplicativos** e, em seguida, **Promapp**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Promapp**.

    ![Lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, selecione **Usuários e grupos**:

    ![Selecionar Usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Selecione Adicionar usuário](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

6. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="just-in-time-user-provisioning"></a>Provisionamento de usuário just-in-time

O Promapp é compatível com o provisionamento de usuário just-in-time. Esse recurso está habilitado por padrão. Se um usuário ainda não existir no Promapp, um novo será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar logon único

Agora você precisará testar a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Promapp no Painel de Acesso, você deverá ser conectado automaticamente à instância do Promapp, para a qual você configurou o SSO. Para obter mais informações sobre o Painel de Acesso, confira [Acessar e usar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png

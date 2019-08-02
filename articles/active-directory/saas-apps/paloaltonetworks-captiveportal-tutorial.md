---
title: 'Tutorial: Integração do Azure Active Directory ao Palo Alto Networks – Captive Portal | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Palo Alto Networks – Captive Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c3651e81cd3916bccc859bc3bc010511b2a114a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095271"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: Integração do Azure Active Directory ao Palo Alto Networks – Captive Portal

Neste tutorial, você aprenderá a integrar o Palo Alto Networks – Captive Portal ao Azure AD (Azure Active Directory).

Você obtém os benefícios a seguir quando integra o Palo Alto Networks – Captive Portal ao Azure AD:

* No Azure AD, é possível controlar quem tem acesso ao Palo Alto Networks – Captive Portal.
* Você pode autorizar os usuários a entrarem automaticamente no Palo Alto Networks – Captive Portal (logon único) usando as respectivas contas do Azure AD.
* É possível gerenciar suas contas em uma, um local central e no portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

Para integrar o Azure AD ao Palo Alto Networks – Captive Portal, você precisa dos itens a seguir:

* Uma assinatura do Azure Active Directory. Se não tiver o Azure AD, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do Palo Alto Networks – Captive Portal habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

O Palo Alto Networks – Captive Portal é compatível com estes cenários:

* **Logon único iniciado por IDP**
* **Provisionamento Just-In-Time do usuário**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Adicionar o Palo Alto Networks – Captive Portal por meio da galeria

Para começar, na galeria, adicione o Palo Alto Networks – Captive Portal à sua lista de aplicativos SaaS gerenciados:

1. No [portal do Azure](https://portal.azure.com), no menu esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![A opção Aplicativos Empresariais no menu](common/enterprise-applications.png)

3. Selecione **Novo aplicativo**.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Palo Alto Networks Captive Portal**. Nos resultados da pesquisa, selecione **Palo Alto Networks – Captive Portal** e, depois, selecione **Adicionar**.

     ![Palo Alto Networks – Captive Portal na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Você configurará e testará o logon único do Azure AD com o Palo Alto Networks – Captive Portal com base em uma usuária de teste chamada *Brenda Fernandes*. Para que o logon único funcione, você deve estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Palo Alto Networks – Captive Portal. 

Para configurar e testar o logon único do Azure AD com o Palo Alto Networks – Captive Portal, realize as tarefas a seguir:

1. **[Configurar logon único do Azure AD](#configure-azure-ad-single-sign-on)** : Habilite o usuário para usar esse recurso.
2. **[Configurar o logon único do Palo Alto Networks – Captive Portal](#configure-palo-alto-networks-captive-portal-single-sign-on)** : Defina as configurações de logon único no aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** : Teste o logon único do Azure AD com a usuária *Brenda Fernandes*.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** : Configure Brenda Fernandes para usar o logon único do Azure AD.
5. **Criar um usuário de teste da Palo Alto Networks – Captive Portal**: Crie um usuário da contraparte *Brenda Fernandes* no Palo Alto Networks – Captive Portal que esteja vinculado ao usuário do Azure AD.
6. **[Testar o logon único](#test-single-sign-on)** : Verifique se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Primeiro, você habilitará o logon único do Azure AD no portal do Azure:

1. No [Portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **Palo Alto Networks – Captive Portal**, selecione **Logon Único**.

    ![Link Configurar logon único](common/select-sso.png)

2. No painel **Selecionar um método de logon único**, selecione **SAML**.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. No painel **Configurar logon único com SAML**, selecione o ícone de lápis **Editar**.

    ![O ícone de lápis Editar](common/edit-urls.png)

4. No painel **Configuração básica de SAML**, conclua as etapas a seguir:

    ![Painel Configuração básica de SAML do Palo Alto Networks – Captive Portal](common/idp-intiated.png)

   1. Para **Identificador**, insira uma URL com o padrão `https://<customer_firewall_host_name>/SAML20/SP`.

   2. Para **URL de resposta**, insira uma URL com o padrão `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Atualize os valores de espaço reservado nesta etapa com o identificador real e URLs de resposta. Para obter os valores reais, entre em contato com a [equipe de suporte ao cliente do Palo Alto Networks – Captive Portal](https://support.paloaltonetworks.com/support).

5. Na seção **Certificado de autenticação SAML**, ao lado de **XML de metadados de federação**, selecione **Baixar**. Salve o arquivo baixado em seu computador.

    ![O link de download do XML de metadados de federação](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Configurar o logon único do Palo Alto Networks – Captive Portal

Em seguida, configure o logon único do Palo Alto Networks – Captive Portal:

1. Em outra janela do navegador, entre no site Palo Alto Networks como administrador.

2. Selecione a guia **Dispositivo**.

    ![Guia Dispositivo do site Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No menu, selecione **Provedor de identidade SAML**e, em seguida, selecione **Importar**.

    ![O botão Importar](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na caixa de diálogo **Importar perfil do servidor do provedor de identidade SAML**, conclua as etapas a seguir:

    ![Configurar o logon único do Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Em **Nome do perfil**, insira um nome, como **AzureAD-CaptivePortal**.
    
    2. Ao lado de **Metadados do provedor de identidade**, selecione **Procurar**. Selecione o arquivo metadata.xml que você baixou no portal do Azure.
    
    3. Selecione **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Em seguida, você criará uma usuária de teste no portal do Azure chamada *Brenda Fernandes*:

1. No portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário**.

    ![O botão Novo usuário](common/new-user.png)

3. No painel **Usuário**, conclua as etapas a seguir:

    ![A caixa de diálogo Usuário](common/user-properties.png)

    1. Em **Nome**, insira **BrendaFernandes**.
  
    2. Em **Nome de usuário**, insira **BrendaFernandes\@\<domínio_da_empresa\>** . Por exemplo, **BrendaFernandes\@contoso.com**.

    3. Em **Senha**, insira uma senha. É recomendável que você mantenha um registro da senha inserida. Você pode marcar a caixa de seleção **Mostrar senha** para exibir a senha.

    4. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Em seguida, conceda acesso ao Palo Alto Networks – Captive Portal para que Brenda Fernandes possa usar o logon único do Azure:

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, insira **Palo Alto Networks – Captive Portal** e, em seguida, selecione o aplicativo.

    ![O link Palo Alto Networks – Captive Portal na lista de Aplicativos](common/all-applications.png)

3. No menu, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Selecione **Adicionar usuário**. Em seguida, no painel **Adicionar atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. No painel **Usuários e grupos**, na lista **Usuários**, selecione **Brenda Fernandes**. Selecione **Selecionar**.

6. Para adicionar um valor de função à declaração SAML, no painel **Selecionar função**, selecione a função relevante para o usuário. Selecione **Selecionar**.

7. No painel **Adicionar atribuição**, selecione **Atribuir**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Criar um usuário de teste do Palo Alto Networks – Captive Portal

Em seguida, crie um usuário chamado *Brenda Fernandes* no Palo Alto Networks – Captive Portal. O Palo Alto Networks – Captive Portal dá suporte ao provisionamento do usuário Just-In-Time, que é habilitado por padrão. Você não precisará concluir as tarefas nesta seção. Se um usuário ainda não existir no Palo Alto Networks – Captive Portal, um novo usuário será criado após a autenticação.

> [!NOTE]
> Se quiser criar um usuário manualmente, entre em contato com a [equipe de suporte ao cliente do Palo Alto Networks – Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Testar logon único 

O Palo Alto Networks – Captive Portal é instalado atrás do firewall em uma VM do Windows. Para testar o logon único no Palo Alto Networks – Captive Portal, entre na VM do Windows usando o protocolo RDP. Na sessão RDP, abra um navegador e acesse qualquer site. A URL de SSO será aberta e você precisará se autenticar. Quando a autenticação for concluída, você poderá acessar sites.

## <a name="additional-resources"></a>Recursos adicionais

Para saber mais, consulte os seguintes artigos:

- [Tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Acesso Condicional no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


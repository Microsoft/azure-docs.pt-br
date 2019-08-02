---
title: 'Tutorial: Integração do Azure Active Directory com Kontiki | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098501"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Integração do Azure Active Directory com Kontiki

Neste tutorial, você aprenderá como integrar o Kontiki ao Azure AD (Azure Active Directory).

A integração do Kontiki ao Azure AD oferece os seguintes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao Kontiki.
* Os usuários podem ser automaticamente conectados ao Kontiki com as contas do Azure AD (logon único).
* Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Kontiki, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do Kontiki com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o Kontiki ao Azure AD.

O Kontiki dá suporte aos seguintes recursos:

* **Logon único iniciado por SP**
* **Provisionamento Just-In-Time do usuário**

## <a name="add-kontiki-in-the-azure-portal"></a>Adicionar o Kontiki no portal do Azure

Para integrar o Kontiki com o Azure AD, você deve adicionar o Kontiki à sua lista de aplicativos SaaS gerenciados.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Active Directory](common/select-azuread.png)

1. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Para adicionar um aplicativo, selecione **Novo aplicativo**.

    ![A opção Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **Kontiki**. Nos resultados da pesquisa, selecione **Kontiki** e, em seguida, **Adicionar**.

    ![Kontiki na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Kontiki, com base em um usuário de teste chamado **Brenda Fernandes**. Para que o logon único funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kontiki.

Para configurar e testar o logon único do Azure AD com o Kontiki, você deve concluir os seguintes blocos de construção:

| Tarefa | DESCRIÇÃO |
| --- | --- |
| **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** | Permite que os usuários usem esse recurso. |
| **[Configurar o logon único do Kontiki](#configure-kontiki-single-sign-on)** | Define as configurações de logon único no aplicativo. |
| **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** | Testa o logon único do Azure AD para um usuário chamado Brenda Fernandes. |
| **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** | Habilita Brenda Fernandes a usar o logon único do Azure AD. |
| **[Criar um usuário de teste do Kontiki](#create-a-kontiki-test-user)** | Cria um equivalente de Brenda Fernandes no Kontiki vinculado à representação do usuário no Azure AD. |
| **[Testar o logon único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você configurará o logon único do Azure AD com o Kontiki no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), no painel de integração de aplicativos do **Kontiki**, clique em **Logon único**.

    ![Opção Configurar logon único](common/select-sso.png)

1. No painel **Selecionar um método de logon único**, selecione o modo **SAML** ou **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. No painel **Configurar logon único com SAML**, selecione **Editar** (o ícone de lápis) para abrir o painel **Configuração Básica de SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. No painel **Configuração Básica de SAML**, na caixa de texto **URL de logon**, digite uma URL com o seguinte padrão: `https://<companyname>.mc.eval.kontiki.com`

    ![Informações de logon único de Domínio e URLs do Kontiki](common/sp-signonurl.png)

    > [!NOTE]
    > Entre em contato com a [equipe de suporte ao cliente do Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html) para obter o valor correto a ser usado. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. No painel **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** ao lado de **XML de Metadados de Federação**. Selecione uma opção de download com base em seus requisitos. Salve o certificado no computador.

    ![A opção de download de certificado XML de Metadados de Federação](common/metadataxml.png)

1. Na seção **Configurar Kontiki**, copie as seguintes URLs com base em seus requisitos:

    * URL de logon
    * Identificador do Azure AD
    * URL de logoff

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Configurar o logon único do Kontiki

Para configurar o logon único no lado do Kontiki, envie o arquivo XML de Metadados de Federação baixado e as URLs relevantes que você copiou do portal do Azure para a [equipe de suporte do Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). A equipe de suporte do Kontiki usa as informações enviadas por você para garantir que a conexão de logon único do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará uma usuária de teste no portal do Azure chamada Brenda Fernandes.

1. No portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

    ![As opções Usuários e Todos os usuários](common/users.png)

1. Selecione **Novo usuário**.

    ![A opção Novo usuário](common/new-user.png)

1. No painel **Usuário**, conclua as etapas a seguir:

    1. Na caixa **Nome**, insira **BrendaFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **brendafernandes\@\<domíniodaempresa>.\<extensão>** . Por exemplo, **brendafernandes\@contoso.com**.

    1. Selecione a caixa de seleção **Mostrar Senha**. Anote o valor exibido na caixa **Senha**.

    1. Selecione **Criar**.

    ![O painel Usuário](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você concederá a Brenda Fernandes acesso ao Kontiki para que ela possa usar o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos** > **Kontiki**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Kontiki**.

    ![Kontiki na lista de aplicativos](common/all-applications.png)

1. No menu, selecione **Usuários e grupos**.

    ![A opção Usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Em seguida, no painel **Adicionar atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No painel **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários. Escolha **Selecionar**.

1. Se você estiver esperando um valor de função na declaração SAML, no painel **Selecionar função**, selecione a função relevante ao usuário na lista. Escolha **Selecionar**.

1. No painel **Adicionar atribuição**, selecione **Atribuir**.

### <a name="create-a-kontiki-test-user"></a>Criar um usuário de teste do Kontiki

Não há nenhum item de ação para a configuração de provisionamento de usuário no Kontiki. Quando um usuário atribuído tenta entrar no Kontiki usando o portal Meus Aplicativos, o Kontiki verifica se o usuário existe. Se nenhuma conta de usuário for encontrada, o Kontiki criará automaticamente a conta de usuário.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Após configurar o logon único, ao selecionar **Kontiki** no portal Meus Aplicativos, você está automaticamente conectado ao Kontiki. Para obter mais informações sobre o portal Meus Aplicativos, confira [Acessar e usar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, examine estes artigos:

- [Lista de tutoriais de integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

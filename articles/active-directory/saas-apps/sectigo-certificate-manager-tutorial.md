---
title: 'Tutorial: Integração do Azure Active Directory com o Sectigo Certificate Manager | Microsoft Docs'
description: ‘Saiba como configurar o logon único entre o Azure Active Directory e o Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588244"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Integração do Azure Active Directory com o Sectigo Certificate Manager

Neste tutorial, você aprenderá como integrar o Sectigo Certificate Manager ao Azure AD (Azure Active Directory).

A integração do Sectigo Certificate Manager ao Azure AD oferece a você os seguintes benefícios:

* É possível usar o Azure AD para controlar quem tem acesso ao Sectigo Certificate Manager.
* Os usuários podem entrar automaticamente no Sectigo Certificate Manager com suas contas do Azure AD (logon único).
* Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Sectigo Certificate Manager, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Assinatura do Sectigo Certificate Manager com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o Sectigo Certificate Manager ao Azure AD.

O Sectigo Certificate Manager dá suporte aos seguintes recursos:

* **Logon único iniciado por SP**
* **Logon único iniciado por IDP**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Adicionar Sectigo Certificate Manager ao portal do Azure

Para integrar o Sectigo Certificate Manager ao Azure AD, você deve adicionar o Sectigo Certificate Manager à sua lista de aplicativos SaaS gerenciados.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Active Directory](common/select-azuread.png)

1. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Para adicionar um aplicativo, selecione **Novo aplicativo**.

    ![A opção Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **Sectigo Certificate Manager**. Nos resultados da pesquisa, selecione **Sectigo Certificate Manager** e, em seguida, selecione **Adicionar**.

    ![Sectigo Certificate Manager na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Sectigo Certificate Manager com base em um usuário de teste chamado **Brenda Fernandes**. Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Sectigo Certificate Manager.

Para configurar e testar o logon único do Azure AD com o Sectigo Certificate Manager, é necessário concluir os seguintes blocos de construção:

| Tarefa | DESCRIÇÃO |
| --- | --- |
| **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** | Permite que os usuários usem esse recurso. |
| **[Configurar o logon único do Sectigo Certificate Manager](#configure-sectigo-certificate-manager-single-sign-on)** | Define as configurações de logon único no aplicativo. |
| **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** | Testa o logon único do Azure AD para um usuário chamado Brenda Fernandes. |
| **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** | Habilita Brenda Fernandes a usar o logon único do Azure AD. |
| **[Criar usuário de teste do Sectigo Certificate Manager](#create-a-sectigo-certificate-manager-test-user)** | Cria um equivalente de Brenda Fernandes no Sectigo Certificate Manager vinculado à representação do usuário no Azure AD. |
| **[Testar o logon único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você configura o logon único do Azure AD com o Sectigo Certificate Manager no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), no painel de integração do aplicativo do **Sectigo Certificate Manager**, selecione **Logon único**.

    ![Opção Configurar logon único](common/select-sso.png)

1. No painel **Selecionar um método de logon único**, selecione o modo **SAML** ou **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. No painel **Configurar logon único com SAML**, selecione **Editar** (o ícone de lápis) para abrir o painel **Configuração Básica de SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. No painel **Configuração Básica de SAML**, para configurar o *modo iniciado por IDP*, conclua as seguintes etapas:

    1. Na caixa **Identificador**, insira uma destas URLs:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Na caixa **URL de resposta**, insira uma destas URLs:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Selecione **Definir URLs adicionais**.

    1. Na caixa **Estado de Retransmissão**, insira uma destas URLs:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Informações de logon único sobre domínio e URLs do Sectigo Certificate Manager](common/idp-relay.png)

1.  Para configurar o aplicativo no *modo iniciado pelo SP*, conclua as seguintes etapas:

    * Na caixa **URL de logon**, insira uma destas URLs:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Informações de logon único sobre domínio e URLs do Sectigo Certificate Manager](common/both-signonurl.png)

1. No painel **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** ao lado de **Certificado (Base64)** . Selecione uma opção de download com base em seus requisitos. Salve o certificado no computador.

    ![A opção de download do Certificado (Base64)](common/certificatebase64.png)

1. Na seção **Configurar Sectigo Certificate Manager**, copie as seguintes URLs com base em seus requisitos:

    * URL de logon
    * Identificador do Azure AD
    * URL de logoff

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configurar o logon único do Sectigo Certificate Manager

Para configurar o logon único no lado do Sectigo Certificate Manager, envie o arquivo de certificado (Base64) baixado e as URLs relevantes que você copiou do portal do Azure para a [equipe de suporte do Sectigo Certificate Manager](https://sectigo.com/support). A equipe de suporte do Sectigo Certificate Manager usa as informações enviadas por você para garantir que a conexão de logon único do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará uma usuária de teste no portal do Azure chamada Brenda Fernandes.

1. No portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

    ![As opções Usuários e Todos os usuários](common/users.png)

1. Selecione **Novo usuário**.

    ![A opção Novo usuário](common/new-user.png)

1. No painel **Usuário**, conclua as etapas a seguir:

    1. Na caixa **Nome**, insira **BrendaFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **brendafernandes\@\<domíniodaempresa>.\<extensão\>** . Por exemplo, **brendafernandes\@contoso.com**.

    1. Selecione a caixa de seleção **Mostrar Senha**. Anote o valor exibido na caixa **Senha**.

    1. Selecione **Criar**.

    ![O painel Usuário](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você concede a Brenda Fernandes acesso ao Sectigo Certificate Manager para que ela possa usar logon único do Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos** > **Sectigo Certificate Manager**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Sectigo Certificate Manager**.

    ![Sectigo Certificate Manager na lista de aplicativos](common/all-applications.png)

1. No menu, selecione **Usuários e grupos**.

    ![A opção Usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Em seguida, no painel **Adicionar atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No painel **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários. Escolha **Selecionar**.

1. Se você estiver esperando um valor de função na declaração SAML, no painel **Selecionar função**, selecione a função relevante ao usuário na lista. Escolha **Selecionar**.

1. No painel **Adicionar atribuição**, selecione **Atribuir**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Criar usuário de teste do Sectigo Certificate Manager

Nesta seção, você criará um usuário chamado Brenda Fernandes no Sectigo Certificate Manager. Trabalhe com a [equipe de suporte do Sectigo Certificate Manager](https://sectigo.com/support) para adicionar o usuário à plataforma do Sectigo Certificate Manager. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Após configurar o logon único, quando você seleciona **Sectigo Certificate Manager** no portal Meus Aplicativos, você é automaticamente conectado ao Sectigo Certificate Manager. Para obter mais informações sobre o portal Meus Aplicativos, confira [Acessar e usar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, examine estes artigos:

- [Lista de tutoriais de integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)



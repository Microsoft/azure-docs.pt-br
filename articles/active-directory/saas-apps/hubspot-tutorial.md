---
title: 'Tutorial: Integração do Azure Active Directory ao HubSpot | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b2c765778fc2bdd8425cc3f375831c0d317e753
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100888"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração do Azure Active Directory ao HubSpot

Neste tutorial, você aprenderá a integrar o HubSpot ao Azure AD (Azure Active Directory).

A integração do HubSpot ao Azure AD oferece os seguintes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao HubSpot.
* Os usuários podem ser automaticamente conectados ao HubSpot com as contas do Azure AD (logon único).
* Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao HubSpot, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do HubSpot com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o HubSpot ao Azure AD.

O HubSpot é compatível com os seguintes recursos:

* **Logon único iniciado por SP**
* **Logon único iniciado por IDP**

## <a name="add-hubspot-in-the-azure-portal"></a>Adicionar o HubSpot no portal do Azure

Para integrar o HubSpot com o Azure AD, você deve adicionar HubSpot à sua lista de aplicativos SaaS gerenciados.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Active Directory](common/select-azuread.png)

1. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Para adicionar um aplicativo, selecione **Novo aplicativo**.

    ![A opção Novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **HubSpot**. Nos resultados da pesquisa, selecione **HubSpot** e, em seguida, **Adicionar**.

    ![HubSpot na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o HubSpot, com base em uma usuária de teste chamada **Brenda Fernandes**. Para que o logon único funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HubSpot.

Para configurar e testar o logon único do Azure AD com o HubSpot, é necessário concluir os seguintes passos básicos:

| Tarefa | DESCRIÇÃO |
| --- | --- |
| **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** | Permite que os usuários usem esse recurso. |
| **[Configurar logon único do HubSpot](#configure-hubspot-single-sign-on)** | Define as configurações de logon único no aplicativo. |
| **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** | Testa o logon único do Azure AD para um usuário chamado Brenda Fernandes. |
| **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** | Habilita Brenda Fernandes a usar o logon único do Azure AD. |
| **[Criar um usuário de teste do HubSpot](#create-a-hubspot-test-user)** | Cria um equivalente de Brenda Fernandes no HubSpot que é vinculado à representação do usuário no Azure AD. |
| **[Testar o logon único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você configurará o logon único do Azure AD com o HubSpot no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), no painel de integração do aplicativo **HubSpot**, selecione **Logon único**.

    ![Opção Configurar logon único](common/select-sso.png)

1. No painel **Selecionar um método de logon único**, selecione o modo **SAML** ou **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. No painel **Configurar logon único com SAML**, selecione **Editar** (o ícone de lápis) para abrir o painel **Configuração Básica de SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. No painel **Configuração Básica de SAML**, para configurar o *modo iniciado por IDP*, conclua as seguintes etapas:

    1. Na caixa **Identificador**, digite uma URL que tenha o seguinte padrão: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>.

    1. Na caixa **URL de resposta**, digite uma URL que tenha o seguinte padrão: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>.

    ![Informações sobre domínio e URLs de logon único do HubSpot](common/idp-intiated.png)

    > [!NOTE]
    > Para formatar as URLs, você também pode ver os padrões exibidos no painel **Configuração Básica de SAML** no portal do Azure.

1. Para configurar o aplicativo no modo *iniciado por SP*:

    1. Selecione **Definir URLs adicionais**.

    1. Na caixa **URL de logon**, digite **https:\//app.hubspot.com/login**.

    ![A opção Definir URLs adicionais](common/metadata-upload-additional-signon.png)

1. No painel **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** ao lado de **Certificado (Base64)** . Selecione uma opção de download com base em seus requisitos. Salve o certificado no computador.

    ![A opção de download do Certificado (Base64)](common/certificatebase64.png)

1. Na seção **Configurar HubSpot**, copie as seguintes URLs com base em seus requisitos:

    * URL de logon
    * Identificador do Azure AD
    * URL de logoff

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Configurar logon único do HubSpot

1. Abra uma nova guia no navegador e entre em sua conta de administrador do HubSpot.

1. Selecione o ícone **Configurações** no canto superior direito da página.

    ![O ícone Configurações no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **Padrões da Conta**.

    ![A opção Padrões da Conta no HubSpot](./media/hubspot-tutorial/config2.png)

1. Role para baixo até a seção **Segurança** e, em seguida, clique em **Configurar**.

    ![A opção Configurar no HubSpot](./media/hubspot-tutorial/config3.png)

1. Na seção **Configurar logon único**, realize as seguintes etapas:

    1. Na caixa **URL do público-alvo (ID de entidade do provedor de serviços)** caixa, selecione **Copiar** para copiar o valor. No portal do Azure, no painel **Configuração Básica de SAML**, cole o valor na caixa **Identificador**.

    1. Na caixa **URL de logon, ACS, destinatário ou o redirecionar**, selecione **Copiar** para copiar o valor. No portal do Azure, no painel **Configuração Básica de SAML**, cole o valor na caixa **URL de Resposta**.

    1. No HubSpot, na caixa **Identificador do Provedor de Identidade ou URL do Emissor**, cole o valor do **Identificador do Azure AD**, que você copiou do portal do Azure.

    1. No HubSpot, na caixa **URL de logon único do Provedor de Identidade**, cole o valor da **URL de logon** que você copiou do portal do Azure.

    1. No bloco de notas do Windows, abra o arquivo do Certificado (Base64) baixado. Selecione e copie o conteúdo do arquivo. Em seguida, no HubSpot, cole-o na caixa **Certificado X.509**.

    1. Selecione **Verificar**.

        ![A seção Configurar logon único no HubSpot](./media/hubspot-tutorial/config4.png)

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

Nesta seção, você permitirá acesso a Brenda Fernandes ao HubSpot para que ela possa usar o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos** > **HubSpot**.

    ![O painel Aplicativos Empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, escolha **HubSpot**.

    ![HubSpot na lista de aplicativos](common/all-applications.png)

1. No menu, selecione **Usuários e grupos**.

    ![A opção Usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Em seguida, no painel **Adicionar atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No painel **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários. Escolha **Selecionar**.

1. Se você estiver esperando um valor de função na declaração SAML, no painel **Selecionar função**, selecione a função relevante ao usuário na lista. Escolha **Selecionar**.

1. No painel **Adicionar atribuição**, selecione **Atribuir**.

### <a name="create-a-hubspot-test-user"></a>Criar um usuário de teste do HubSpot

Para habilitar um usuário do Azure AD a entrar no HubSpot, o usuário deve ser provisionado no HubSpot. No HubSpot, o provisionamento é uma tarefa manual.

Para provisionar uma conta de usuário no HubSpot:

1. Entre no site do HubSpot da sua empresa como administrador.

1. Selecione o ícone **Configurações** no canto superior direito da página.

    ![O ícone Configurações no HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **Usuários e Equipes**.

    ![A opção Usuários e Equipes em HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecione **Criar usuário**.

    ![A opção Criar usuário no HubSpot](./media/hubspot-tutorial/user2.png)

1. Na caixa **Adicionar endereço(s) de email**, digite o endereço de email do usuário no formato brendafernandes\@contoso.com e, em seguida, selecione **Avançar**.

    ![A caixa Adicionar endereços de email, na seção Criar usuários, no HubSpot](./media/hubspot-tutorial/user3.png)

1. Na seção **Criar usuários**, selecione cada guia. Em cada guia, defina as opções e permissões relevantes para o usuário. Em seguida, selecione **Avançar**.

    ![Guias na seção Criar usuários no HubSpot](./media/hubspot-tutorial/user4.png)

1. Para enviar o convite para o usuário, selecione **Enviar**.

    ![A opção Enviar no HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > O usuário é ativado depois que o usuário aceitar o convite.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Após configurar o logon único, ao selecionar **HubSpot** no portal Meus Aplicativos, você está automaticamente conectado ao HubSpot. Para obter mais informações sobre o portal Meus Aplicativos, confira [Acessar e usar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, examine estes artigos:

- [Lista de tutoriais de integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Logon único em aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Tutorial: Integração do Azure Active Directory com o Sectigo Certificate Manager | Microsoft Docs'
description: ‘Saiba como configurar o logon único entre o Azure Active Directory e o Sectigo Certificate Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: c589db84b6221aa23868b3b49aea84f33623619f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673858"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Integração do Azure Active Directory com o Sectigo Certificate Manager

Neste tutorial, você aprenderá como integrar o Sectigo Certificate Manager (também chamado de SCM) ao Azure AD (Azure Active Directory).

A integração do Sectigo Certificate Manager ao Azure AD oferece a você os seguintes benefícios:

* É possível usar o Azure AD para controlar quem tem acesso ao Sectigo Certificate Manager.
* Os usuários podem entrar automaticamente no Sectigo Certificate Manager com suas contas do Azure AD (logon único).
* Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Sectigo Certificate Manager, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Conta do Sectigo Certificate Manager.

> [!NOTE]
> O Sectigo executa várias instâncias do Sectigo Certificate Manager. A principal instância do Sectigo Certificate Manager é **https:\//cert-manager.com**, e essa URL é usada neste tutorial.  Se sua conta estiver em uma instância diferente, você precisará ajustar as URLs de acordo.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o Sectigo Certificate Manager ao Azure AD.

O Sectigo Certificate Manager dá suporte aos seguintes recursos:

* **Logon único iniciado por SP**
* **Logon único iniciado por IDP**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Adicionar Sectigo Certificate Manager ao portal do Azure

Para integrar o Sectigo Certificate Manager ao Azure AD, você deve adicionar o Sectigo Certificate Manager à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com).

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

| Tarefa | Descrição |
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

1. Na seção **Configuração Básica do SAML**, conclua as seguintes etapas:

    1. Na caixa **Identificador (ID da Entidade)** , para a instância principal do Sectigo Certificate Manager, insira **https:\//cert-manager.com/shibboleth**.

    1. Na caixa **URL de Resposta**, para a instância principal do Sectigo Certificate Manager, insira **https:\//cert-manager.com/Shibboleth.sso/SAML2/POST**.
        
    > [!NOTE]
    > Embora, em geral, a **URL de Logon** seja obrigatória para o *modo iniciado por SP*, não é necessário fazer logon no Sectigo Certificate Manager.        

1. Opcionalmente, na seção **Configuração Básica do SAML**, para configurar o *modo iniciado por IDP* e para permitir que o **Teste** funcione, conclua as seguintes etapas:

    1. Selecione **Definir URLs adicionais**.

    1. Na caixa **Estado de Retransmissão**, insira a URL específica do cliente do Sectigo Certificate Manager. Para a instância principal do Sectigo Certificate Manager, insira **https:\//cert-manager.com/customer/\<customerURI\>/idp**.

    ![Informações de logon único sobre domínio e URLs do Sectigo Certificate Manager](common/idp-relay.png)

1. Na caixa de diálogo **Declarações e Atributos do Usuário**, conclua as seguintes etapas:

    1. Exclua todas as **Declarações adicionais**.
    
    1. Selecione **Adicionar nova declaração** e adicione as seguintes quatro declarações:
    
        | Nome | Namespace | Fonte | Atributo de origem | Descrição |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | vazio | Atributo | user.userprincipalname | Precisa corresponder ao campo **ID da Pessoa do IdP** no Sectigo Certificate Manager para Administradores. |
        | mail | vazio | Atributo | user.mail | Obrigatório |
        | givenName | vazio | Atributo | user.givenname | Opcional |
        | sn | vazio | Atributo | user.surname | Opcional |

       ![Sectigo Certificate Manager – Adicionar quatro novas declarações](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. Na seção **Certificado de Autenticação SAML**, selecione **Baixar** ao lado de **XML de Metadados de Federação**. Salve o arquivo XML em seu computador.

    ![A opção de download do XML de Metadados de Federação](common/metadataxml.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Configurar o logon único do Sectigo Certificate Manager

Para configurar o logon único no lado do Sectigo Certificate Manager, envie o arquivo XML de Metadados de Federação baixado para a [equipe de suporte do Sectigo Certificate Manager](https://sectigo.com/support). A equipe de suporte do Sectigo Certificate Manager usa as informações enviadas por você para garantir que a conexão de logon único do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará uma usuária de teste no portal do Azure chamada Brenda Fernandes.

1. No portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

    ![As opções Usuários e Todos os usuários](common/users.png)

1. Selecione **Novo usuário**.

    ![A opção Novo usuário](common/new-user.png)

1. No painel **Usuário**, conclua as etapas a seguir:

    1. Na caixa **Nome**, insira **BrendaFernandes**.
  
    1. Na caixa **Nome de usuário**, insira **brendafernandes\@\<your-company-domain>.\<extension\>** . Por exemplo, **brendafernandes\@contoso.com**.

    1. Selecione a caixa de seleção **Mostrar Senha**. Registre o valor exibido na caixa **Senha**.

    1. Selecione **Criar**.

    ![O painel Usuário](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você concede a Brenda Fernandes acesso ao Sectigo Certificate Manager para que a usuária possa usar o logon único do Azure.

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

Nesta seção, você testará sua configuração de logon único do Azure AD.

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Fazer teste do Sectigo Certificate Manager (logon único iniciado por SP)

Navegue até a URL específica do cliente (para a instância principal do Sectigo Certificate Manager, https:\//cert-manager.com/customer/\<customerURI\>/) e selecione o botão abaixo **Ou Entre Com**.  Se configurado corretamente, você será conectado automaticamente ao Sectigo Certificate Manager.

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Fazer teste da configuração de logon único do Azure (logon único iniciado por IdP)

No painel de integração de aplicativos do **Sectigo Certificate Manager**, selecione **Logon único** e o botão **Testar**.  Se configurado corretamente, você será conectado automaticamente ao Sectigo Certificate Manager.

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>Testar usando o portal Meus Aplicativos (logon único iniciado por IdP)

Selecione **Sectigo Certificate Manager** no portal Meus Aplicativos.  Se configurado corretamente, você será conectado automaticamente ao Sectigo Certificate Manager. Para obter mais informações sobre o portal Meus Aplicativos, confira [Acessar e usar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, examine estes artigos:

- [Lista de tutoriais de integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)
- [Logon único em aplicativos no Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
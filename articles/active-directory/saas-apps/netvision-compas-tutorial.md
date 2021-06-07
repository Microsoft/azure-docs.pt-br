---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Netvision Compas | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Netvision Compas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 423ac882c81582f2843eeba37d11c660662ad6e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519394"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Netvision Compas

Neste tutorial, você aprenderá a integrar o Netvision Compas ao Azure AD (Azure Active Directory). Ao integrar o Netvision Compas ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Netvision Compas.
* Permitir que os usuários sejam conectados automaticamente ao Netvision Compas com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Netvision Compas.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Netvision Compas dá suporte ao SSO iniciado por **SP e IDP**
* Depois de configurar o Netvision Compas, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Adicionar o Netvision Compas da galeria

Para configurar a integração do Netvision Compas ao Azure AD, você precisará adicionar o Netvision Compas da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Netvision Compas** na caixa de pesquisa.
1. Selecione **Netvision Compas** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Configurar e testar o logon único do Azure AD para o Netvision Compas

Configure e teste o SSO do Azure AD com o Netvision Compas usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Netvision Compas.

Para configurar e testar o SSO do Azure AD com o Netvision Compas, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Netvision Compas](#configure-netvision-compas-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Configurar um usuário de teste do Netvision Compas](#configure-netvision-compas-test-user)** : para ter um equivalente de B.Fernandes no Netvision Compas que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Netvision Compas**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<TENANT>.compas.cloud/Identity/Saml20`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do Netvision Compas](mailto:contact@net.vision) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o arquivo de metadados e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)



### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Netvision Compas.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Netvision Compas**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-netvision-compas-sso"></a>Configurar o SSO do Netvision Compas

Nesta seção, você habilitará o SSO do SAML no **Netvision Compas**.
1. Faça logon no **Netvision Compas** usando uma conta administrativa e acesse a área de administração.

    ![Área de administração](media/netvision-compas-tutorial/admin.png)

1. Localize a área do **Sistema** e selecione **Provedores de Identidade**.

    ![IdPs administradores](media/netvision-compas-tutorial/admin-idps.png)

1. Selecione a ação **Adicionar** para registrar o Azure AD como um novo IdP.

    ![Adicionar IdP](media/netvision-compas-tutorial/idps-add.png)

1. Selecione **SAML** como o **Tipo de provedor**.
1. Insira valores significativos nos campos **Nome de exibição** e **Descrição**.
1. Atribua os usuários **Netvision Compas** ao IdP selecionando-os na lista **Usuários disponíveis** e, em seguida, escolhendo o botão **Adicionar selecionados**. Os usuários também podem ser atribuídos ao IdP durante o procedimento de provisionamento.
1. Para a opção **Metadados** do SAML, clique no botão **Escolher Arquivo** e escolha o arquivo de metadados salvo anteriormente no computador.
1. Clique em **Save** (Salvar).

    ![Editar IdP](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Configurar um usuário de teste do Netvision Compas

Nesta seção, você vai configurar um usuário existente no **Netvision Compas** para que ele use o Azure AD para SSO.
1. Siga o procedimento de provisionamento de usuário do **Netvision Compas**, conforme definido pela sua empresa ou edite uma conta de usuário existente.
1. Ao definir o perfil do usuário, verifique se o endereço de **Email (Pessoal)** do usuário corresponde ao nome de usuário do Azure AD: username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.

    ![Editar usuário](media/netvision-compas-tutorial/user-config.png)

Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Como usar o Painel de Acesso (iniciado por IdP).

Ao clicar no bloco Netvision Compas no Painel de Acesso, você deverá ser conectado automaticamente ao Netvision Compas para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Como obter acesso direto ao Netvision Compas (iniciado por SP).

1. Acesse a URL do **Netvision Compas**. Por exemplo, `https://tenant.compas.cloud`.
1. Insira o nome de usuário do **Netvision Compas** e escolha **Avançar**.

    ![Usuário de logon](media/netvision-compas-tutorial/login-user.png)

1. **(opcional)** Se o usuário receber vários IdPs no **Netvision Compas**, uma lista de IdPs disponíveis será apresentada. Selecione o IdP do Azure AD configurado anteriormente no **Netvision Compas**.

    ![Escolha de logon](media/netvision-compas-tutorial/login-choose.png)

1. Você será redirecionado ao Azure AD para realizar a autenticação. Depois de ser autenticado com êxito, você deverá ser conectado automaticamente ao **Netvision Compas**, para o qual configurou o SSO.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Netvision Compas com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
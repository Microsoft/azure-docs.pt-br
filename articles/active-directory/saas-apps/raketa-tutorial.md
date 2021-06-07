---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Raketa | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Raketa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 649a26e474c7c4d6b4f51a035b3f8f9da35b9dd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511147"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Raketa

Neste tutorial, você aprenderá a integrar o Raketa ao Azure AD (Azure Active Directory). Quando integrar o Raketa ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Raketa.
* Permitir que os usuários sejam conectados automaticamente ao Raketa com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Raketa.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Raketa dá suporte ao SSO iniciado por **SP**.
* Depois de configurar o Raketa, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-raketa-from-the-gallery"></a>Adicionar o Raketa da galeria

Para configurar a integração do Raketa ao Azure AD, você precisará adicionar o Raketa da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, escolha o serviço **Azure Active Directory** [1].

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. Navegue até **Aplicativos Empresariais** [2] e escolha **Todos os Aplicativos** [3].

1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** [4]. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. Na seção **Adicionar por meio da galeria** [5], digite **Raketa** na caixa de pesquisa [6].

1. Selecione **Raketa** no painel de resultados [7] e clique no botão **Adicionar** [8]. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Configurar e testar o logon único do Azure AD para o Raketa

Configure e teste o SSO do Azure AD com o Raketa usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Raketa.

Para configurar e testar o SSO do Azure AD com o Raketa, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Raketa](#configure-raketa-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Raketa](#create-raketa-test-user)** – para ter um equivalente de B.Fernandes no Raketa que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Raketa**, localize a seção **Gerenciar** e selecione **Logon único** [9].

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. Na página **Selecionar um método de logon único** [9], selecione **SAML** [10].

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** [11] para editar as configurações.

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    1. Nas caixas de texto **Identificador (ID da Entidade)** [12] e **URL de Logon** [14], digite a URL `https://raketa.travel/`.

    1. Na caixa de texto **URL de Resposta** [13], digite uma URL usando o seguinte padrão: `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>`.  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize o valor com a URL de Resposta real. Para obter o valor, entre em contato com a [equipe de suporte ao cliente do Raketa](mailto:help@raketa.travel). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** [15] para baixar o certificado e salvá-lo no computador.

1. Na seção **Configurar o Raketa**, copie as URLs apropriadas de acordo com suas necessidades.

    1. URL de Logon [16] – A URL da página da Web de autorização, que é usada para redirecionar os usuários para o sistema de autenticação.

    1. Identificador do Azure AD [17] – Identificador do Azure AD.

    1. URL de Logoff [18] – A URL da página da Web, que é usada para redirecionar os usuários após o logoff.

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** [1], **Usuários** [19] e **Todos os usuários** [20].

1. Selecione **Novo usuário** [21] na parte superior da tela.

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. Nas propriedades do **Usuário**, siga estas etapas:

   1. No campo **Nome de usuário** [22], insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.

   1. No campo **Nome** [23], insira `B.Simon`.

   1. Marque a caixa de seleção **Mostrar senha** [25] e anote o valor exibido na caixa **Senha** [24].

   1. Clique em **Criar** [26]. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Raketa.

1. No portal do Azure, escolha **Aplicativos Empresariais** [2] e **Todos os aplicativos** [3].

1. Na lista de aplicativos, escolha **Raketa** [27].  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos** [28]. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. Escolha **Adicionar usuário** [29] e **Usuários e grupos** [30] na caixa de diálogo **Adicionar Atribuição**.

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** [31] na lista Usuários e clique no botão **Selecionar** [32] na parte inferior da tela.

1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.

1. Na caixa de diálogo **Adicionar Atribuição**, clique no botão **Atribuir** [33]. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Configurar o SSO do Raketa

Para configurar o logon único no lado do **Raketa**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Raketa](mailto:help@raketa.travel). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-raketa-test-user"></a>Criar um usuário de teste do Raketa

Nesta seção, você criará um usuário com o nome B.Fernandes no Raketa. Trabalhe com a [equipe de suporte do Raketa](mailto:help@raketa.travel) para adicionar os usuários à plataforma Raketa. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Raketa no Painel de Acesso, você deverá ser conectado automaticamente ao Raketa, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Avaliar o Raketa com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Raketa com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
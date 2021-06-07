---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Parkalot – Sistema de gerenciamento de estacionamento | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Parkalot – Sistema de gerenciamento de estacionamento.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 0098d28d2b211ad9e4bbe60a44c5de4058f2b96b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651392"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Parkalot – Sistema de gerenciamento de estacionamento

Neste tutorial, você aprenderá a integrar o Parkalot – Sistema de gerenciamento de estacionamento ao Azure AD (Azure Active Directory). Ao integrar o Parkalot – Sistema de gerenciamento de estacionamento ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Parkalot – Sistema de gerenciamento de estacionamento.
* Permitir que os usuários, usando as respectivas contas do Azure AD, sejam conectados automaticamente ao Parkalot – Sistema de gerenciamento de estacionamento.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Parkalot – Sistema de gerenciamento de estacionamento.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Parkalot – Sistema de gerenciamento de estacionamento dá suporte ao SSO iniciado por **SP**

* O Parkalot – Sistema de gerenciamento de estacionamento dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>Como adicionar o Parkalot – Sistema de gerenciamento de estacionamento por meio da galeria

Para configurar a integração do Parkalot – Sistema de gerenciamento de estacionamento ao Azure AD, você precisará adicionar o Parkalot – Sistema de gerenciamento de estacionamento por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Parkalot – Sistema de gerenciamento de estacionamento** na caixa de pesquisa.
1. Selecione **Parkalot – Sistema de gerenciamento de estacionamento** do painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>Configurar e testar o SSO do Azure AD para o Parkalot – Sistema de gerenciamento de estacionamento

Configure e teste o SSO do Azure AD com o Parkalot – Sistema de gerenciamento de estacionamento usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Parkalot – Sistema de gerenciamento de estacionamento.

Para configurar e testar o SSO do Azure AD com o Parkalot – Sistema de gerenciamento de estacionamento, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Parkalot – Sistema de gerenciamento de estacionamento](#configure-parkalot-car-park-management-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Parkalot – Sistema de gerenciamento de estacionamento](#create-parkalot-car-park-management-test-user)** – para ter um equivalente de B. Fernandes no Parkalot – Sistema de gerenciamento de estacionamento que esteja vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Parkalot – Sistema de gerenciamento de estacionamento**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando um dos seguintes padrões:

    | Identificador (ID da Entidade) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. Na caixa de texto **URL de Resposta**, digite uma URL nos seguintes padrões:

    | URL de resposta |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. Na caixa de texto **URL de Logon**, digite uma URL usando um dos seguintes padrões:

    | URL de logon |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [Equipe de suporte ao cliente do Parkalot – Sistema de gerenciamento de estacionamento](mailto:contact-us@parkalot.io) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar Parkalot – Sistema de gerenciamento de estacionamento**, copie as URLs apropriadas de acordo com os seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure permitindo que ela tenha acesso ao Parkalot – Sistema de gerenciamento de estacionamento.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Parkalot – Sistema de gerenciamento de estacionamento**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-parkalot-car-park-management-sso"></a>Configurar o SSO do Parkalot – Sistema de gerenciamento de estacionamento

1. Em uma janela de navegador da Web diferente, entre no site da sua empresa do Parkalot – Sistema de gerenciamento de estacionamento como administrador.

1. Selecione **Configurar SAML** e clique no ícone **Editar** no cartão **Adicionar Novo**.

    ![Ícone EDITAR em Adicionar Novo.](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. Execute as etapas mencionadas abaixo na página a seguir.

    ![Configure o SSO do Parkalot – Sistema de gerenciamento de estacionamento.](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. Na caixa de texto **Nome de Exibição**, forneça um nome válido a ele.

    b. Na caixa de texto **ID da Entidade do IdP**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de SSO**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado**.

    e. Clique em **SALVAR**.

### <a name="create-parkalot-car-park-management-test-user"></a>Criar um usuário de teste do Parkalot – Sistema de gerenciamento de estacionamento

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Parkalot – Sistema de gerenciamento de estacionamento. O Parkalot – Sistema de gerenciamento de estacionamento dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Parkalot – Sistema de gerenciamento de estacionamento, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Parkalot – Sistema de gerenciamento de estacionamento, na qual será possível iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Parkalot – Sistema de gerenciamento de estacionamento e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Parkalot – Sistema de gerenciamento de estacionamento em Meus Aplicativos, será redirecionado para a URL de Logon do Parkalot – Sistema de gerenciamento de estacionamento. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Parkalot – Sistema de gerenciamento de estacionamento, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
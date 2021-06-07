---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao IBM Digital Business Automation on Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o IBM Digital Business Automation on Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: jeedes
ms.openlocfilehash: 91a50ed85b5802657a27c5a545de0013937e9da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460556"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ibm-digital-business-automation-on-cloud"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory ao IBM Digital Business Automation on Cloud

Neste tutorial, você aprenderá a integrar o IBM Digital Business Automation on Cloud ao Azure AD (Azure Active Directory). Ao integrar o IBM Digital Business Automation on Cloud ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao IBM Digital Business Automation on Cloud.
* Permitir que os usuários sejam conectados automaticamente ao IBM Digital Business Automation on Cloud com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do IBM Digital Business Automation on Cloud com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O IBM Digital Business Automation on Cloud dá suporte ao SSO iniciado por **SP e IDP**
* Depois de configurar o IBM Digital Business Automation on Cloud, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ibm-digital-business-automation-on-cloud-from-the-gallery"></a>Adicionar o IBM Digital Business Automation on Cloud por meio da galeria

Para configurar a integração do IBM Digital Business Automation on Cloud ao Azure AD, você precisará adicionar o IBM Digital Business Automation on Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **IBM Digital Business Automation on Cloud** na caixa de pesquisa.
1. Selecione **IBM Digital Business Automation on Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ibm-digital-business-automation-on-cloud"></a>Configurar e testar o logon único do Azure AD para o IBM Digital Business Automation on Cloud

Configure e teste o SSO do Azure AD com o IBM Digital Business Automation on Cloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do IBM Digital Business Automation on Cloud.

Para configurar e testar o SSO do Azure AD com o IBM Digital Business Automation on Cloud, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do IBM Digital Business Automation on Cloud](#configure-ibm-digital-business-automation-on-cloud-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do IBM Digital Business Automation on Cloud](#create-ibm-digital-business-automation-on-cloud-test-user)** – para ter um equivalente de B.Fernandes no IBM Digital Business Automation on Cloud que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **IBM Digital Business Automation on Cloud**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:
    
    a. Clique em **Carregar arquivo de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão populados automaticamente na caixa de texto da seção do IBM Digital Business Automation on Cloud:

    > [!Note]
    > Se os valores de **Identificador** e **URL de Resposta** não forem populados automaticamente, preencha-os manualmente de acordo com suas necessidades.

    > [!Note]
    > Os clientes podem obter o arquivo de metadados para a assinatura de nuvem na [equipe de suporte ao cliente do IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com).

1. Se você não tiver o **arquivo de metadados do Provedor de Serviços**, na seção **Configuração SAML Básica**, se desejar configurar o aplicativo no modo iniciado por **IDP**, digite os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/saml20/login`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://www.automationcloud.ibm.com/isam/sps/<TENANT>/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o IBM Digital Business Automation on Cloud**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao IBM Digital Business Automation on Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **IBM Digital Business Automation on Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ibm-digital-business-automation-on-cloud-sso"></a>Configurar o SSO do IBM Digital Business Automation on Cloud

Para configurar o logon único no lado do **IBM Digital Business Automation on Cloud**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do IBM Digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-ibm-digital-business-automation-on-cloud-test-user"></a>Criar um usuário de teste do IBM Digital Business Automation on Cloud

Nesta seção, você criará um usuário chamado Brenda Fernandes no IBM Digital Business Automation on Cloud. Trabalhe com a [equipe de suporte do IBM digital Business Automation on Cloud](mailto:supportbpmoncloud@us.ibm.com) para adicionar os usuários na plataforma do IBM Digital Business Automation on Cloud. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do IBM Digital Business Automation on Cloud no Painel de Acesso, você deverá ser conectado automaticamente ao IBM Digital Business Automation on Cloud para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o IBM Digital Business Automation on Cloud com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o IBM Digital Business Automation on Cloud com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
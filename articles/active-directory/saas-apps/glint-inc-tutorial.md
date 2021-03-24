---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Glint Inc | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Glint Inc.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 9bf523fcc7699096b273b79d703770cbe71dcb35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92448564"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-glint-inc"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Glint Inc

Neste tutorial, você aprenderá a integrar o Glint Inc ao Azure AD (Azure Active Directory). Ao integrar o Glint Inc ao Azure AD, você poderá:

* Controlar quem tem acesso ao Glint Inc no Azure AD.
* Permitir que os seus usuários entrem automaticamente no Glint Inc usando as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Glint Inc.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Glint Inc dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-glint-inc-from-the-gallery"></a>Como adicionar o Glint Inc por meio da galeria

Para configurar a integração do Glint Inc ao Azure AD, você precisará adicionar o Glint Inc da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Glint Inc** na caixa de pesquisa.
1. Selecione **Glint Inc** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-glint-inc"></a>Configurar e testar o SSO do Azure AD para o Glint Inc

Configure e teste o SSO do Azure AD com o Glint Inc usando um usuário de teste com o nome de **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Glint Inc.

Para configurar e testar o SSO do Azure AD com o Glint Inc, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Glint Inc](#configure-glint-inc-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Glint Inc](#create-glint-inc-test-user)** – para ter um equivalente de B. Fernandes no Glint Inc que esteja vinculado à declaração do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Glint Inc**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.<REGION>.glintinc.com/api/client/<CUSTOMER_NAME>/token/saml2/consume/includeDeskLink`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.<REGION>.glintinc.com/api/client/<CUSTOMER_NAME>/token/saml2/consume/includeDeskLink`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://api.<REGION>.glintinc.com/api/client/<CUSTOMER_NAME>/token/saml2/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do Glint Inc](mailto:glint-ssosupport@linkedin.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Glint Inc**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo o acesso ao Glint Inc.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Glint Inc**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-glint-inc-sso"></a>Configurar o SSO do Glint Inc

Para configurar o logon único no lado do **Glint Inc**, é necessário enviar o **XML de Metadados Federados** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Glint Inc](mailto:glint-ssosupport@linkedin.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-glint-inc-test-user"></a>Criar um usuário de teste do Glint Inc

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Glint Inc. Trabalhe com a [equipe de suporte do Glint Inc](mailto:glint-ssosupport@linkedin.com) para adicionar usuários na plataforma do Glint Inc. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Glint Inc, na qual você poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do Glint Inc diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IDP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no Glint Inc para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco Glint Inc no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Glint Inc para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Glint Inc, você poderá impor o controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
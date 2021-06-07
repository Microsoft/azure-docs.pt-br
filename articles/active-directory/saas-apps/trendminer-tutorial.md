---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TrendMiner | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TrendMiner.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: jeedes
ms.openlocfilehash: 7f7e7126c482038907e5e986d0779827957cb093
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182165"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trendminer"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TrendMiner

Neste tutorial, você aprenderá a integrar o TrendMiner ao Azure AD (Azure Active Directory). Com a integração do TrendMiner ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao TrendMiner.
* Permitir que os usuários sejam conectados automaticamente ao TrendMiner com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do TrendMiner.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O TrendMiner é compatível com SSO iniciado por **SP e IDP**

## <a name="adding-trendminer-from-the-gallery"></a>Como adicionar o TrendMiner por meio da galeria

Para configurar a integração do TrendMiner com o Azure AD, você precisa adicionar o TrendMiner da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **TrendMiner** na caixa de pesquisa.
1. Selecione **TrendMiner** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-trendminer"></a>Configurar e testar o SSO do Azure AD para o TrendMiner

Configure e teste o SSO do Azure AD com o TrendMiner usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TrendMiner.

Para configurar e testar o SSO do Azure AD com o TrendMiner, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do TrendMiner](#configure-trendminer-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do TrendMiner](#create-trendminer-test-user)** – para ter um equivalente de B.Fernandes no TrendMiner que esteja vinculado à representação da usuária no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **TrendMiner**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CUSTOMER>.trendminer.cloud/security/saml/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CUSTOMER>.trendminer.cloud/security/saml/SSO`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<CUSTOMER>.trendminer.cloud/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do TrendMiner](mailto:support@trendminer.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o TrendMiner**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao TrendMiner.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TrendMiner**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-trendminer-sso"></a>Configurar o SSO do TrendMiner

1. Abra uma nova janela do navegador da Web e entre em seu site de empresa do TrendMiner como administrador.

1. No menu à esquerda, selecione **SEGURANÇA > Provedor de Identidade**

1. Na página **Provedor de Identidade**, clique primeiro em **SAML** e depois em **Próxima etapa**.

    ![selecionar SAML](./media/trendminer-tutorial/saml.png)

1. Clique em **Próxima etapa** na página **Backup do SAML**.

    ![selecionar Backup do SAML](./media/trendminer-tutorial/saml-backup.png)

1. Clique em **Próxima etapa** para **Certificados autoassinados**.

    ![Página Certificados autoassinados](./media/trendminer-tutorial/self-signed-certificate.png)

1. Você pode **Ignorar** o upload de uma chave de assinatura.

    ![carregar uma chave de assinatura](./media/trendminer-tutorial/signing-key.png)

1. Na tela **Configuração do SAML** na **URL base da entidade**, insira a URL do domínio como `https://trendminer.domain.com/`

1. Em **Metadados do provedor de identidade**, carregue o **arquivo de metadados do Azure** que você copiou do portal do Azure e, em seguida, clique em **Próxima etapa**.

    ![Configuração de SAML](./media/trendminer-tutorial/saml-configuration.png)

1. Na seção **Mapeamento de usuário do SAML**, insira os nomes de usuário que serão usados para fazer logon e clique em **Concluir**.

    ![Mapeamento de usuário do SAML](./media/trendminer-tutorial/user-mapping.png)

### <a name="create-trendminer-test-user"></a>Criar usuário de teste do TrendMiner

Nesta seção, você criará uma usuária chamada Brenda Fernandes no TrendMiner. Trabalhe com a [equipe de suporte do TrendMiner](mailto:support@trendminer.com) para adicionar os usuários à plataforma TrendMiner. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do TrendMiner, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do TrendMiner, na qual você iniciará o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao TrendMiner para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco TrendMiner no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao TrendMiner para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o TrendMiner, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
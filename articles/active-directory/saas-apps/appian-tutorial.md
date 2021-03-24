---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Appian | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Appian.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: jeedes
ms.openlocfilehash: aef8eb429bf772dff4d4b0de629cb1118b140e10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180380"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appian"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Appian

Neste tutorial, você aprenderá a integrar o Appian ao Azure AD (Azure Active Directory). Quando você integra o Appian ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao Appian.
* Permitir que os usuários sejam conectados automaticamente ao Appian com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Appian habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Appian é compatível com o SSO iniciado por **SP e IdP**
* O Appian é compatível com o provisionamento de usuário **just-in-time**

## <a name="adding-appian-from-the-gallery"></a>Como adicionar o Appian da galeria

Para configurar a integração do Appian com o Azure AD, será necessário adicionar o Appian da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Appian** na caixa de pesquisa.
1. Selecione **Appian** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-appian"></a>Configurar e testar o SSO do Azure AD para o Appian

Configure e teste o SSO do Azure AD com o Appian usando um usuário de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Appian.

Para configurar e testar o SSO do Azure AD com o Appian, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Appian](#configure-appian-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Appian](#create-appian-test-user)** : para ter um equivalente de B.Fernandes no Appian vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Appian**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.appiancloud.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.appiancloud.com/suite/saml/AssertionConsumer`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.appiancloud.com/suite`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao Cliente do Appian](mailto:support@appian.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Appian**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Appian.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Appian**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-appian-sso"></a>Configurar o SSO do Appian

1. Em outra janela do navegador da Web, entre no site do Appian como administrador.

1. Acesse o **Console de Administração** e selecione **Habilitar SAML**.

1. Clique em **Adicionar Provedor de Identidade SAML**.

1.  Insira uma **Descrição** para o IdP.

1.  Insira um **Nome do Provedor de Serviços**; ele deverá ser um nome exclusivo para os provedores de serviço e identidade.

1.  Carregue os metadados do IdP para os **Metadados do Provedor de Identidade**. 

1.  Escolha o **Algoritmo de Hash de Assinatura** apropriado correspondente ao IdP.

1.  Clique em **Testar Esta Configuração** do lado superior direito da caixa de diálogo para verificar se a configuração é válida ou não.

1. Clique em **Concluído** depois de entrar com êxito para fechar a caixa de diálogo de configuração.

1. Se você quiser que a página de entrada do novo IdP seja o padrão, defina a **Página de Entrada Padrão** de acordo.

1.  Clique em **Verificar Meus Acessos** depois que a caixa de diálogo for fechada para que você ainda possa entrar no Appian. Desta vez, você precisa entrar com o usuário atual.

1.  Depois de verificar com êxito se você ainda pode entrar, clique em **Salvar Alterações**.


### <a name="create-appian-test-user"></a>Criar um usuário de teste do Appian

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Appian. O Appian é compatível com o provisionamento de usuário just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Appian, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Appian, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Appian e inicie o fluxo de logon de lá.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Appian para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Appian no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Appian para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Appian, você poderá impor o controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
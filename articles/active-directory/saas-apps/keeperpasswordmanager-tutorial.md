---
title: 'Tutorial: Integração do Azure Active Directory ao Gerenciador de Senhas Protetor e Cofre Digital | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Gerenciador de Senhas Protetor e Cofre Digital.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96177977"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Tutorial: integração do Azure Active Directory ao Gerenciador de Senhas Protetor e Cofre Digital

Neste tutorial, você aprenderá a integrar o Gerenciador de Senhas Protetor e o Cofre Digital ao Azure Active Directory (Azure AD).
A integração fornece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Keeper Password Manager & Digital Vault.
* Você pode permitir que os usuários sejam conectados automaticamente com o Keeper Password Manager & Digital Vault (Logon Único) usando as contas do Azure AD deles.
* Gerencie suas contas em um único local: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Keeper Password Manager & Digital Vault, você precisa de:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, obtenha uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura do Keeper Password Manager & Digital Vault habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Keeper Password Manager & Digital Vault é compatível com o SSO iniciado por SP.

* O Keeper Password Manager & Digital Vault é compatível com o provisionamento de usuário just-in-time.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionar o Keeper Password Manager & Digital Vault por meio da galeria

Para configurar a integração do Keeper Password Manager & Digital Vault ao Azure AD, adicione o aplicativo da galeria à sua lista de aplicativos SaaS (software como serviço) gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Microsoft Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Em **Adicionar da galeria**, digite **Keeper Password Manager & Digital Vault** na caixa de pesquisa.
1. Selecione **Keeper Password Manager & Digital Vault** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Configurar e testar o SSO do Azure AD para o Keeper Password Manager & Digital Vault

Configure e teste o SSO do Azure AD com o Keeper Password Manager & Digital Vault usando um usuário de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação vinculada entre um usuário do Azure AD e o usuário relacionado do Keeper Password Manager & Digital Vault.

Para configurar e testar o SSO do Azure AD com o Keeper Password Manager & Digital Vault, execute as seguintes etapas:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.

    * [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
    * [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.

1. [Configurar o SSO do Keeper Password Manager & Digital Vault](#configure-keeper-password-manager--digital-vault-sso) para definir as configurações de SSO no lado do aplicativo.
    * [Criar um usuário de teste do Keeper Password Manager & Digital Vault](#create-a-keeper-password-manager--digital-vault-test-user) – para ter um equivalente de Brenda Fernandes no Keeper Password Manager & Digital Vault vinculado à representação da usuária do Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Keeper Password Manager & Digital Vault**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o logon único com o SAML, com o ícone de lápis realçado.](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, realize as seguintes etapas:

    a. Para **URL de Logon**, insira uma URL que use o seguinte padrão:
    * Para o SSO de nuvem: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Para o SSO local: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Para **Identificador (ID da Entidade)** , insira uma URL que use o seguinte padrão:
    * Para o SSO de nuvem: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Para o SSO local: `https://<KEEPER_FQDN>/sso-connect`

    c. Para **URL de Resposta**, digite uma URL que use o seguinte padrão:
    * Para o SSO de nuvem: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Para o SSO local: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com as informações reais de URL de logon, Identificador e URL de Resposta. Para obter esses valores, entre em contato com a [equipe de suporte ao Cliente do Keeper Password Manager & Digital Vault Client](https://keepersecurity.com/contact.html). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Keeper Password Manager & Digital Vault espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela de Atributos do Usuário e Declarações.](common/default-attributes.png)

1. Além disso, o aplicativo Keeper Password Manager & Digital Vault espera que mais alguns atributos sejam passados novamente na resposta SAML. Eles são mostrados na tabela a seguir. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ------------| --------- |
    | Primeiro | user.givenname |
    | Último | user.surname |
    | Email | user.mail |

5. Em **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar**. Isso baixa o **XML de Metadados de Federação** das opções de acordo com suas necessidades e o salva em seu computador.

    ![Captura de tela de Certificado de Autenticação SAML, com Download realçado.](common/metadataxml.png)

6. Em **Configurar o Keeper Password Manager & Digital Vault**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Captura de tela de Configurar o Keeper Password Manager & Digital Vault, com URLs realçadas.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará um usuário de teste no portal do Azure chamado `B.Simon`.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Na parte superior da tela, selecione **Novo usuário**.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. Para **Nome**, insira `B.Simon`.  
   1. Para **Nome de usuário**, insira `username@companydomain.extension`. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar senha** e anote o valor da senha mostrada.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Keeper Password Manager & Digital Vault.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Gerenciador de Senhas Protetor e Cofre Digital**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Selecione **Adicionar usuário**. Em **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Em **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, a função **Acesso Padrão** será selecionada.
1. Em **Adicionar Atribuição**, selecione **Atribuir**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Configurar o SSO do Keeper Password Manager & Digital Vault

Para configurar o SSO para o aplicativo, confira as diretrizes no [guia de suporte do Keeper](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Criar um usuário de teste do Keeper Password Manager & Digital Vault

Para permitir que os usuários do Azure AD entrem no Keeper Password Manager & Digital Vault, você precisa provisioná-los. O aplicativo é compatível com o provisionamento de usuário just-in-time e, após a autenticação, os usuários são criados no aplicativo automaticamente. Se quiser configurar usuários manualmente, entre em contato com o [suporte do Keeper](https://keepersecurity.com/contact.html).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* No portal do Azure, selecione **Testar este aplicativo**. Isso redireciona você à URL de logon do Keeper Password Manager & Digital Vault, na qual poderá iniciar o logon. 

* Você pode acessar diretamente a URL de logon do aplicativo e iniciar a entrada de lá.

* Use o Painel de Acesso da Microsoft. Quando você selecionar o bloco do **Keeper Password Manager & Digital Vault** no Painel de Acesso, isso redirecionará você à URL de logon do aplicativo. Para obter mais informações sobre o Painel de Acesso, confira [Entrar e iniciar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Keeper Password Manager & Digital Vault, você pode impor o controle de sessão. Isso protege contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. Para obter mais informações, confira [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
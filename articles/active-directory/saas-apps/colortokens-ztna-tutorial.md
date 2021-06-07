---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o ColorTokens ZTNA   Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ColorTokens ZTNA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: c87eb3b52843f4de39d0c037bdf452160be1ca2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735951"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-colortokens-ztna"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ColorTokens ZTNA

Neste tutorial, você aprenderá a integrar o ColorTokens ZTNA ao Azure AD (Azure Active Directory). Com a integração do ColorTokens ZTNA ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao ColorTokens ZTNA.
* Permitir que os usuários sejam conectados automaticamente ao ColorTokens ZTNA com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do ColorTokens ZTNA habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ColorTokens ZTNA dá suporte ao SSO iniciado por **SP**

## <a name="adding-colortokens-ztna-from-the-gallery"></a>Adicionando o ColorTokens ZTNA da galeria

Para configurar a integração do ColorTokens ZTNA ao Azure AD, você precisará adicionar o ColorTokens ZTNA da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **ColorTokens ZTNA** na caixa de pesquisa.
1. Selecione **ColorTokens ZTNA** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-colortokens-ztna"></a>Configurar e testar o SSO do Azure AD para o ColorTokens ZTNA

Configure e teste o SSO do Azure AD com o ColorTokens ZTNA usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ColorTokens ZTNA.

Para configurar e testar o SSO do Azure AD com o ColorTokens ZTNA, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ColorTokens ZTNA](#configure-colortokens-ztna-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do ColorTokens ZTNA](#create-colortokens-ztna-test-user)** – para ter um equivalente de B.Fernandes no ColorTokens ZTNA que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **ColorTokens ZTNA**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenantname>.spectrum.colortokens.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Contate a [equipe de suporte ao cliente do ColorTokens ZTNA](mailto:support@colortokens.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Seu aplicativo ColorTokens ZTNA espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo ColorTokens ZTNA espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ------------- | --------- |
    | department | user.userprincipalname |
    | Agrupar | user.groups |

    > [!NOTE]
    > Clique [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) para saber como criar funções no Azure AD.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o ColorTokens ZTNA**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao ColorTokens ZTNA.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ColorTokens ZTNA**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você tiver configurado as funções conforme explicado acima, será possível selecioná-las na lista suspensa **Selecionar uma função**.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-colortokens-ztna-sso"></a>Configurar o SSO do ColorTokens ZTNA

Para configurar o logon único no lado do **ColorTokens ZTNA**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do ColorTokens ZTNA](mailto:support@colortokens.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-colortokens-ztna-test-user"></a>Criar usuário de teste do ColorTokens ZTNA

Nesta seção, você criará um usuário chamado Brenda Fernandes no ColorTokens ZTNA. Trabalhe com a [equipe de suporte do ColorTokens ZTNA](mailto:support@colortokens.com) para adicionar os usuários na plataforma do ColorTokens ZTNA. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do ColorTokens ZTNA, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do ColorTokens ZTNA, na qual você iniciará o fluxo de logon.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do ColorTokens ZTNA em Meus Aplicativos, você será redirecionado para a URL de Logon do ColorTokens ZTNA. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o ColorTokens ZTNA, você poderá impor controles de sessão, o que protege contra o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

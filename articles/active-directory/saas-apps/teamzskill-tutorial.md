---
title: 'Tutorial: integração do SSO (logon único) do Azure Active Directory ao TeamzSkill | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TeamzSkill.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 22e237a3451c87fd4867949b9aa94d19b6fcbbdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729421"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamzskill"></a>Tutorial: integração do SSO (logon único) do Azure Active Directory ao TeamzSkill

Neste tutorial, você aprenderá a integrar o TeamzSkill ao Azure AD (Azure Active Directory). Ao integrar o TeamzSkill ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao TeamzSkill.
* Permitir que os usuários sejam conectados automaticamente ao TeamzSkill com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO do TeamzSkill.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O TeamzSkill dá suporte ao SSO iniciado por **SP e IdP**
* O TeamzSkill dá suporte ao provisionamento de usuário **just-in-time**

## <a name="adding-teamzskill-from-the-gallery"></a>Adicionar o TeamzSkill da galeria

Para configurar a integração do TeamzSkill ao Azure AD, você precisará adicionar o TeamzSkill da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **TeamzSkill** na caixa de pesquisa.
1. Selecione **TeamzSkill** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-teamzskill"></a>Configurar e testar o SSO do Azure AD para o TeamzSkill

Configure e teste o SSO do Azure AD com o TeamzSkill usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TeamzSkill.

Para configurar e testar o SSO do Azure AD com o TeamzSkill, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do TeamzSkill](#configure-teamzskill-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do TeamzSkill](#create-teamzskill-test-user)** – para ter um equivalente de B.Fernandes no TeamzSkill vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **TeamzSkill**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [Equipe de suporte ao cliente do TeamzSkill](mailto:support@teamzskill.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo TeamzSkill espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo TeamzSkill espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ------------ | --------- |
    | Firstname | user.givenname |
    | Sobrenome | user.surname |
    | jobtitle | user.jobtitle |
    | department | user.department |
    | employeeid | user.employeeid |
    | postalcode | user.postalcode |
    | country | user.country |
    | função | user.assignedroles |

    > [!NOTE]
    > O TeamzSkill espera funções para os usuários atribuídos ao aplicativo. Configure essas funções no Azure AD para que os usuários possam ser atribuídos às funções apropriadas. Para entender como configurar funções no Azure AD, confira [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o TeamzSkill**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao TeamzSkill.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **TeamzSkill**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você tiver configurado as funções conforme explicado acima, será possível selecioná-las na lista suspensa **Selecionar uma função**.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-teamzskill-sso"></a>Configurar o SSO do TeamzSkill

1. Em outra janela do navegador da Web, entre no TeamzSkill como administrador.

1. Clique no ícone Perfil do usuário e selecione **Configurações da empresa**.

    ![Configurações da empresa no Teamzskill](./media/teamzskill-tutorial/settings.png)

1. Execute as etapas a seguir na página **Configurações**.

    ![configurações no Teamzskill](./media/teamzskill-tutorial/metadata.png)

    a. Navegue até **Empresa > Logon único** e selecione a guia **Carregar Metadados**.

    b. Cole o valor de **XML de Metadados de Federação** que você copiou do portal do Azure no campo de **Metadados XML**.
     
    c. Em seguida, clique em **Salvar**.

### <a name="create-teamzskill-test-user"></a>Criar usuário de teste do TeamzSkill

Nesta seção, um usuário chamado B.Fernandes será criado no TeamzSkill. O TeamzSkill dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se nenhum usuário existir quando você tentar acessar o TeamzSkill, ele será criado.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do TeamzSkill, na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do TeamzSkill diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no TeamzSkill para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco TeamzSkill no Painel de Acesso, se estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se estiver configurado no modo IDP, você será conectado automaticamente ao TeamzSkill para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o TeamzSkill, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

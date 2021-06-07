---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao BIC Cloud Design | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BIC Cloud Design.
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
ms.openlocfilehash: 7a706eb120f45fcd08febd0bda9aaaa54a82135a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728158"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bic-cloud-design"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao BIC Cloud Design

Neste tutorial, você aprenderá a integrar o BIC Cloud Design ao Azure AD (Azure Active Directory). Ao integrar o BIC Cloud Design ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao BIC Cloud Design.
* Permitir que os usuários sejam conectados automaticamente ao BIC Cloud Design com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do BIC Cloud Design.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O BIC Cloud Design é compatível com SSO iniciado pelo **SP**

## <a name="adding-bic-cloud-design-from-the-gallery"></a>Como adicionar o BIC Cloud Design por meio da galeria

Para configurar a integração do BIC Cloud Design ao Azure AD, você precisará adicionar o BIC Cloud Design por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **BIC Cloud Design** na caixa de pesquisa.
1. Selecione **BIC Cloud Design** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-bic-cloud-design"></a>Configurar e testar o SSO do Azure AD para o BIC Cloud Design

Configure e teste o SSO do Azure AD com o BIC Cloud Design usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no BIC Cloud Design.

Para configurar e testar o SSO do Azure AD com o BIC Cloud Design, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do BIC Cloud Design](#configure-bic-cloud-design-sso)** : para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do BIC Cloud Design](#create-bic-cloud-design-test-user)** : para ter um equivalente de B.Fernandes no BIC Cloud Design que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **BIC Cloud Design**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, o valor de **Identificador** será populado automaticamente na seção Configuração Básica do SAML.

    ![Informações de logon único de Domínio e URLs do BIC Cloud Design](common/sp-identifier.png)

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:

    ```https
    https://<customer-specific-name/tenant>.biccloud.com
    https://<customer-specific-name/tenant>.biccloud.de
    ```

    > [!Note]
    > Se o valor de **Identificador** não for preenchido de modo automático, preencha o valor manualmente de acordo com suas necessidades. O valor da URL de logon não é real. Atualize esse valor com a URL de Entrada real. Contate a [equipe de suporte ao cliente do BIC Cloud Design](mailto:bicsupport@gbtec.de) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo BIC Cloud Design espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo BIC Cloud Design espera que mais alguns atributos sejam transmitidos novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ------------ | --------- |
    | Nome | user.name |
    | Endereço de email | user.mail |
    | ID do nome | user.userprincipalname |
    | email | user.mail |
    | nametest | user.displayname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao BIC Cloud Design.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **BIC Cloud Design**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-bic-cloud-design-sso"></a>Configurar o SSO do BIC Cloud Design

Para configurar o logon único no **BIC Cloud Design**, é necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do BIC Cloud Design](mailto:bicsupport@gbtec.de). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-bic-cloud-design-test-user"></a>Criar um usuário de teste do BIC Cloud Design

Nesta seção, você criará um usuário chamado B.Fernandes no BIC Cloud Design. Trabalhe com a [equipe de suporte do BIC Cloud Design](mailto:bicsupport@gbtec.de) para adicionar os usuários na plataforma do BIC Cloud Design. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do BIC Cloud Design na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do BIC Cloud Design diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do BIC Cloud Design em Meus Aplicativos, isso redirecionará para a URL de logon do BIC Cloud design. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o BIC Cloud Design, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
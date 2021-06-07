---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Tableau Server | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Tableau Server.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 810f57afceda10c10cf5919a0c50ca6be2950703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650576"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Tableau Server

Neste tutorial, você aprenderá a integrar o Tableau Server ao Azure AD (Azure Active Directory). Com a integração do Tableau Server ao Azure AD, você poderá:

* Controlar no Microsoft Azure AD quem tem acesso ao Tableau Server.
* Permitir que seus usuários entrem automaticamente no Tableau Server com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Tableau Server habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Tableau Server é compatível com SSO iniciado por **SP**

## <a name="add-tableau-server-from-the-gallery"></a>Adicionar o Tableau Server por meio da galeria

Para configurar a integração do Tableau Server ao Azure AD, você precisa adicionar o Tableau Server da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Tableau Server** na caixa de pesquisa.
1. Selecione **Tableau Server** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-tableau-server"></a>Configurar e testar o SSO do Azure AD para o Tableau Server

Configure e teste o SSO do Azure AD com o Tableau Server usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tableau Server.

Para configurar e testar o SSO do Azure AD com o Tableau Server, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Tableau Server](#configure-tableau-server-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Tableau Server](#create-tableau-server-test-user)** – para ter um equivalente de B. Fernandes no Tableau Server que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Tableau Server**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://azure.<domain name>.link`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://azure.<domain name>.link`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Os valores anteriores não são valores reais. Atualize os valores com os valores reais de URL de Logon, Identificador e URL de Resposta na página de configuração do Tableau Server, explicada posteriormente no tutorial.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Tableau Server**, copie a URL apropriada, com base em suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Tableau Server.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Tableau Server**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-tableau-server-sso"></a>Configurar o SSO do Tableau Server

1. Para configurar o SSO para seu aplicativo, você precisa entrar no locatário Tableau Server como administrador.

2. Na guia **CONFIGURAÇÃO**, selecione **Identidade e acesso do usuário** e, em seguida, selecione a guia Método de **Autenticação**.

    ![A captura de tela mostra a opção Autenticação selecionada em Identidade do Usuário e Acesso.](./media/tableauserver-tutorial/auth.png)

3. Na página **CONFIGURAÇÃO**, realize as seguintes etapas:

    ![Captura de tela que mostra a página Configuração, na qual você pode inserir os valores descritos.](./media/tableauserver-tutorial/config.png)

    a. Para **Método de Autenticação**, selecione SAML.

    b. Selecione a caixa de seleção **Habilitar a autenticação SAML para o servidor**.

    c. URL de retorno do Tableau Server — a URL que os usuários do Tableau Server acessarão, por exemplo `http://tableau_server`. Usar `http://localhost` não é recomendado. Não há suporte para usar uma URL com uma barra à direita (por exemplo, `http://tableau_server/`). Copie **URL de retorno do Tableau Server** e cole-o na caixa de texto **URL de Logon** na seção **Configuração Básica de SAML** no portal do Azure.

    d. ID de entidade SAML - a ID de entidade identifica com exclusividade sua instalação do Tableau Server para o IdP. Você pode digitar a URL do Tableau Server novamente aqui, se desejar, mas ela não precisa ser sua URL do Tableau Server. Copie **ID da entidade SAML** e cole-a na caixa de texto **Identificador** na seção **Configuração Básica de SAML** no portal do Azure.

    e. Clique em **Baixar Arquivo de Metadados XML** e abra-o no aplicativo de editor de texto. Localize a URL do Serviço de Declaração do Consumidor com Http Post e Índice 0 e copie a URL. Agora, cole-o na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    f. Localize o arquivo de Metadados de Federação baixado do portal do Azure e carregue-o no **arquivo de metadados do IDP do SAML**.

    g. Insira os nomes dos atributos que o IdP usa para manter os nomes de usuário, nomes de exibição e endereços de email.

    h. Clique em **Save** (Salvar).

    > [!NOTE]
    > O cliente precisa carregar um arquivo de Certificado X509 codificado por PEM com extensão .crt e um arquivo de chave privada RSA ou DSA com extensão .key, como um arquivo de Chave de Certificado. Para obter mais informações sobre o arquivo de Certificado e o arquivo de Chave de Certificado, confira [este](https://help.tableau.com/current/server/en-us/saml_requ.htm) documento. Se precisar de ajuda para configurar o SAML no Tableau Server, confira o artigo [Configurar SAML no âmbito do servidor](https://help.tableau.com/current/server/en-us/config_saml.htm).

### <a name="create-tableau-server-test-user"></a>Criar um usuário de teste do Tableau Server

O objetivo desta seção é criar uma usuária chamada B. Fernandes no Tableau Server. Será necessário provisionar todos os usuários no Tableau Server.

O nome de usuário deve corresponder ao valor que você configurou no atributo personalizado **username** do Azure AD. Com o mapeamento correto, a integração deve funcionar, conforme a Configuração do Logon Único do Azure AD.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com o administrador do Tableau Server em sua organização.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Tableau Server, na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Tableau Server diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Tableau Server em Meus Aplicativos, isso redirecionará você à URL de logon do Tableau Server. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Tableau Server, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
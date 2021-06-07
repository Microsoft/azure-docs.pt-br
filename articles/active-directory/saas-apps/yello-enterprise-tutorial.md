---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory com o Yello Enterprise | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Yello Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: dc9c8e622840d30f1d18ab783cc20e2659a1b5e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-yello-enterprise"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Yello Enterprise

Neste tutorial, você aprenderá a integrar o Yello Enterprise ao Azure AD (Azure Active Directory). Ao integrar o Yello Enterprise ao Azure AD, será possível:

* Controlar quem tem acesso ao Yello Enterprise no Azure AD.
* Permitir que os usuários sejam conectados ao Yello Enterprise de modo automático usando contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para o SSO (logon único) do Yello Enterprise.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Yello Enterprise é compatível com um SSO iniciado por **IDP**

## <a name="adding-yello-enterprise-from-the-gallery"></a>Como adicionar o Yello Enterprise da galeria

Será necessário adicionar o Yello Enterprise da galeria à sua lista de aplicativos SaaS gerenciados para configurar a integração dele com o Azure AD.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Yello Enterprise** na caixa de pesquisa.
1. Selecione **Yello Enterprise** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-yello-enterprise"></a>Configurar e testar o SSO do Azure AD para o Yello Enterprise

Configure e teste o SSO do Azure AD usando o Yello Enterprise com um usuário de teste chamado **B.Fernandes**. Será necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Yello Enterprise para que o SSO funcione.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o Yello Enterprise:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Yello Enterprise](#configure-yello-enterprise-sso)** – para definir as configurações de logon único no aplicativo.
    1. **[Criar um usuário de teste do Yello Enterprise](#create-yello-enterprise-test-user)** – para ter um equivalente a B.Fernandes no Yello Enterprise que esteja vinculado à representação de um usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Yello Enterprise**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.yello.co/<IDP_NAME>`

    > [!NOTE]
    > Esse valor não é real. Atualize o valor com o identificador real. Entre em contato com a [equipe de suporte ao cliente do Yello Enterprise](mailto:support@yello.co) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Yello Enterprise espera que as declarações SAML estejam em um formato específico. Isso exigirá adicionar mapeamentos de atributos personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Yello Enterprise espera que mais alguns atributos sejam passados novamente na resposta SAML. Esses atributos serão mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ------------ | --------- |
    | ID do funcionário | user.employeeid |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Yello Enterprise**, copie as URLs apropriadas de acordo com seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use um logon único do Azure, concedendo acesso ao Yello Enterprise.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Yello Enterprise**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-yello-enterprise-sso"></a>Configurar um SSO do Yello Enterprise

Para configurar um logon único no **Yello Enterprise**, será necessário enviar o **XML de Metadados Federados** baixado, além de URLs apropriadas e copiadas do portal do Azure para a [equipe de suporte do Yello Enterprise](mailto:support@yello.co). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-yello-enterprise-test-user"></a>Criar um usuário de teste do Yello Enterprise

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Yello Enterprise. Trabalhe com a [equipe de suporte do Yello Enterprise](mailto:support@yello.co) para adicionar usuários à plataforma do Yello Enterprise. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você entrará de modo automático no Yello Enterprise para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Yello Enterprise em Meus Aplicativos, você entrará de modo automático no Yello Enterprise para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Yello Enterprise, será possível impor um controle de sessão que fornecerá proteção contra a exfiltração e a infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do Azure Active Directory com o xMatters OnDemand | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: 762bd1c536df0ca307149ba7c201f08f5bdfded5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735582"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Integração do Azure Active Directory com o xMatters OnDemand

Neste tutorial, você aprenderá como integrar o xMatters OnDemand ao Azure Active Directory (Azure AD).
A integração do xMatters OnDemand ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao xMatters OnDemand.
* Você pode permitir que os usuários façam logon automaticamente no xMatters OnDemand (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o xMatters OnDemand, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do xMatters OnDemand habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O xMatters OnDemand dá suporte ao SSO iniciado por **IDP**

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Adicionar o xMatters OnDemand da galeria

Para configurar a integração do xMatters OnDemand com o Azure AD, é necessário adicionar o xMatters OnDemand da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **xMatters OnDemand** na caixa de pesquisa.
1. Selecione **xMatters OnDemand** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Configurar e testar o SSO do Azure AD para xMatters OnDemand

Configure e teste o SSO do Azure AD com o xMatters OnDemand usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do xMatters OnDemand.

Para configurar e testar o SSO do Azure AD com o xMatters OnDemand, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    2. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do xMatters OnDemand](#configure-xmatters-ondemand-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do xMatters OnDemand](#create-xmatters-ondemand-test-user)** – para ter um equivalente de Brenda Fernandes no xMatters OnDemand vinculado à representação de usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **xMatters OnDemand**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL usando um dos seguintes padrões:

    | Identificador |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. Na caixa de texto **URL de Resposta**, digite uma URL nos seguintes padrões:

    | URL de resposta |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do xMatters OnDemand](https://www.xmatters.com/company/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

    > [!IMPORTANT]
    > É necessário encaminhar o certificado para a [equipe de suporte do xMatters OnDemand](https://www.xmatters.com/company/contact-us/). O certificado precisa ser carregado pela equipe de suporte do xMatters antes de você finalizar a configuração de logon único.

6. Na seção **Configurar o xMatters OnDemand**, copie a URL apropriada de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao xMatters OnDemand.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **xMatters OnDemand**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.


## <a name=&quot;configure-xmatters-ondemand-sso&quot;></a>Configurar o SSO do xMatters OnDemand

1. Em uma janela diferente do navegador da Web, entre no seu site de empresa no xMatters OnDemand como administrador.

2. Clique em **Administração** e em **Detalhes da Empresa**.

    ![Página de administração](./media/xmatters-ondemand-tutorial/admin.png &quot;Administrador")

3. Na página **Configuração do SAML** , realize as seguintes etapas:

    ![Seção de configuração do SAML ](./media/xmatters-ondemand-tutorial/saml-configuration.png "Configuração do SAML")

    a. Selecione **Habilitar SAML**.

    b. Na caixa de texto **ID do Provedor de Identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logon Único**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    d. Na caixa de texto **Redirecionamento da URL de Logoff**, cole **URL de Logoff**, que você copiou do portal do Azure.

    e. Clique em **Escolher Arquivo** para carregar o **Certificado (Base64)** que você baixou do portal do Azure. 

    f. Na página de detalhes da empresa, na parte superior, clique em **Salvar alterações**.

    ![Detalhes da empresa](./media/xmatters-ondemand-tutorial/save-button.png "Detalhes da empresa")

### <a name="create-xmatters-ondemand-test-user"></a>Criar usuário de teste do xMatters OnDemand

1. Entre em seu locatário do **xMatters OnDemand**.

2. Acesse o **Ícone de Usuários** > **Usuários** e clique em **Adicionar Usuários**.

    ![Usuários](./media/xmatters-ondemand-tutorial/add-user.png "Usuários")

3. Na seção **Adicionar Usuários**, preencha os campos obrigatórios e clique no botão **Adicionar Usuário**.

    ![Adicionar um Usuário](./media/xmatters-ondemand-tutorial/add-user-2.png "Adicionar um Usuário")



### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você deverá ser conectado automaticamente ao xMatters OnDemand para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Quando clicar no bloco do xMatters OnDemand no portal Meus Aplicativos, você deverá ser automaticamente conectado ao xMatters OnDemand para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o xMatters OnDemand, será possível impor o controle de sessão, que fornece proteção contra a infiltração e exfiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
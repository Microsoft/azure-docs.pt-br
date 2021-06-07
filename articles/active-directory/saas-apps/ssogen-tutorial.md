---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9eeafaf0f5fbfaff9394ced0a0623f2fb462ed4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646981"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE

Neste tutorial, você aprenderá a integrar o SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE ao Azure AD (Azure Active Directory). Ao integrar o SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE.
* Permitir que os usuários sejam conectados automaticamente ao SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE dá suporte ao SSO iniciado por **SP e IDP**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Adicionar o SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE por meio da galeria

Para configurar a integração do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE ao Azure AD, é necessário adicionar o SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE** na caixa de pesquisa.
1. Selecione **SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Configurar e testar o SSO do Azure AD para o SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE

Configure e teste o SSO do Azure AD com o SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE.

Para configurar e testar o SSO do Azure AD com o SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE](#configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE](#create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user)** : para ter um equivalente de B.Fernandes no SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE](mailto:support@ssogen.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE espera que as declarações SAML estejam em um formato específico, o que exige a adição mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE espera que **nameidentifier** seja mapeado com **user.onpremisessamaccountname**; portanto, é necessário editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso"></a>Configurar o SSO do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE

Para configurar o logon único no lado do **SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE**, encontre a documentação de registro do SSO específica do aplicativo abaixo:

* Integração do SSO do Azure AD – Oracle EBS: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* Integração do SSO do Azure AD – PeopleSoft: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* Integração do SSO do Azure AD – JD Edwards: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Integração do SSO do Azure AD – Apache: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user"></a>Criar um usuário de teste do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE

O Azure AD envia a ID de Nome (identificador de usuário único) para o aplicativo de usuário depois que a autenticação é bem-sucedida.  Verifique se a ID de Nome (identificador de usuário único) corresponde ao registro de usuário no aplicativo, FND_USER.USER_NAME no Oracle EBS, por exemplo.

Entre em contato pelos emails [info@ssogen.com](mailto:info@ssogen.com) e [support@ssogen.com](mailto:support@ssogen.com) para obter qualquer tipo de suporte.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Você será redirecionado à URL de Logon do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE, na qual poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você entrará automaticamente no SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você entrará automaticamente no SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SSOGEN – Azure AD SSO Gateway para Oracle E-Business Suite – EBS, PeopleSoft e JDE, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
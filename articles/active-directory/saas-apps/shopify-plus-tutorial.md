---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Shopify Plus | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Shopify Plus.
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
ms.openlocfilehash: 65f4963f23d97ca2e3af34febb0d5dbea652fc12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646955"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Shopify Plus

Neste tutorial, você aprenderá a integrar o Shopify Plus ao Azure AD (Azure Active Directory). Ao integrar o Shopify Plus ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Shopify Plus.
* Permitir que os usuários sejam conectados automaticamente ao Shopify Plus com as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Shopify Plus.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Shopify Plus é compatível com SSO iniciado por **SP e IDP**.

## <a name="add-shopify-plus-from-the-gallery"></a>Adicionar o Shopify Plus por meio da galeria

Para configurar a integração do Shopify Plus ao Azure AD, você precisará adicionar o Shopify Plus por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Shopify Plus** na caixa de pesquisa.
1. Selecione **Shopify Plus** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-shopify-plus"></a>Configurar e testar o SSO do Azure AD para o Shopify Plus

Configure e teste o SSO do Azure AD com o Shopify Plus com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Shopify Plus.

Para configurar e testar o SSO do Azure AD com o Shopify Plus, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Shopify Plus](#configure-shopify-plus-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Shopify Plus](#create-shopify-plus-test-user)** – para ter um equivalente de B.Fernandes no Shopify Plus que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Shopify Plus**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://shopify.plus/login`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte ao cliente do Shopify Plus](mailto:plus-user-management@shopify.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Shopify Plus espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Shopify Plus espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ---- | --------------- |
    | email | user.mail |

1. Altere o formato da **ID do Nome** para **Persistente**. Selecione a opção **Identificador Exclusivo de Usuário (ID do Nome)** e escolha o formato de **Identificador de nome**. Selecione **Persistente** nessa opção. Salve suas alterações.
1. Na página **Configurar logon único com o SAML**, na seção **Certificado de Autenticação SAML**, selecione o botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder-lhe acesso ao Shopify Plus.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Shopify Plus**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-shopify-plus-sso"></a>Configurar o SSO do Shopify Plus

Para ver as etapas completas, confira a [documentação do Shopify sobre como configurar as integrações do SAML](https://help.shopify.com/en/manual/shopify-plus/saml).

Para configurar o logon único no lado do **Shopify Plus**, copie a **URL de Metadados de Federação do Aplicativo** do Azure Active Directory. Em seguida, faça logon no [administrador da organização](https://shopify.plus) e acesse **Usuários** > **Segurança**. Selecione **Definir configuração** e cole a URL de Metadados de Federação do Aplicativo na seção **URL de metadados do provedor de identidade**. Selecione **Adicionar** para concluir esta etapa.

### <a name="create-shopify-plus-test-user"></a>Criar usuário de teste do Shopify Plus

Nesta seção, você criará um usuário chamado B.Fernandes no Shopify Plus. Volte à seção **Usuários** e adicione um usuário inserindo o email e as permissões deles. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="enforce-saml-authentication"></a>Impor a autenticação SAML

> [!NOTE]
> Recomendamos testar a integração utilizando usuários individuais antes de aplicá-la em grande escala.

Usuários individuais:
1. Acesse a página de um usuário individual no Shopify Plus com um domínio de email gerenciado pelo Azure AD e confirmado no Shopify Plus.
1. Na seção de autenticação SAML, selecione **Editar**, escolha **Obrigatória** e selecione **Salvar**.
1. Teste se esse usuário pode se conectar com êxito por meio dos fluxos iniciados pelo IdP e pelo SP.

Para todos os usuários em um domínio de email:
1. Volte à página **Segurança**.
1. Selecione **Obrigatória** para a configuração de autenticação SAML. Isso impõe o SAML a todos os usuários com esse domínio de email no Shopify Plus.
1. Clique em **Salvar**.

> [!IMPORTANT]
> A habilitação do SAML para todos os usuários em um domínio de email afeta todos os usuários que usam esse aplicativo. Os usuários não poderão se conectar usando a respectiva página de entrada normal. Eles só poderão acessar o aplicativo por meio do Azure Active Directory. O Shopify não fornece uma URL de conexão de backup na qual os usuários podem entrar usando o nome de usuário e a senha normais. Entre em contato com o suporte do Shopify para desativar o SAML, se necessário.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Shopify Plus, na qual poderá iniciar o fluxo de logon.  

* Acesse a URL de Logon do Shopify Plus diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e entre automaticamente no Shopify Plus para o qual você configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco do Shopify Plus nos Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente ao Shopify Plus para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o Shopify Plus, você poderá impor controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
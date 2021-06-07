---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Smartlook | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Smartlook.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: jeedes
ms.openlocfilehash: 056585355242de20e543265b3d8b06863e434f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955560"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartlook"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Smartlook

Neste tutorial, você aprenderá a integrar o Smartlook ao Azure AD (Azure Active Directory). Ao integrar o Smartlook ao Azure AD, será possível:

* Controlar quem terá acesso ao Smartlook no Azure AD.
* Permitir que os usuários sejam conectados de modo automático ao Smartlook usando as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Smartlook.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Smartlook é compatível com um SSO iniciado por **SP e IDP**
* O Smartlook é compatível com o provisionamento de usuário **Just-In-Time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-smartlook-from-the-gallery"></a>Como adicionar o Smartlook da galeria

Para configurar a integração do Smartlook no Azure AD, será preciso adicionar o Smartlook da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Smartlook** na caixa de pesquisa.
1. Selecione **Smartlook** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-smartlook"></a>Configurar e testar o SSO do Azure AD para o Smartlook

Configure e teste o SSO do Azure AD usando o Smartlook com um usuário de teste chamado **B.Simon**. Para que o SSO funcione, será preciso estabelecer uma relação de vínculo entre um usuário do Azure AD e um usuário relacionado no Smartlook.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o Smartlook:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Smartlook](#configure-smartlook-sso)** – para definir configurações de logon único no aplicativo.
    1. **[Criar um usuário de teste do Smartlook](#create-smartlook-test-user)** – para ter um equivalente a B.Simon no Smartlook que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Smartlook**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://app.smartlook.com/sign/sso`

1. Clique em **Save** (Salvar).

1. O aplicativo Smartlook espera que as declarações SAML estejam em um formato específico. Isso exige que você adicione mapeamentos de atributos personalizados à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Smartlook espera que mais alguns atributos sejam passados de volta na resposta SAML. Esses atributos serão mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ------------------- | --------- |
    | urn:oasis:names:tc:SAML:attribute:subject-id | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    |

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

Nesta seção, você permitirá que B.Simon use o logon único do Azure, concedendo acesso ao Smartlook.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Selecione a opção **Smartlook** na lista de aplicativos.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-smartlook-sso"></a>Configurar o SSO do Smartlook

Para configurar o logon único no **Smartlook**, será preciso enviar a **URL de Metadados Federados do Aplicativo** para a [equipe de suporte do Smartlook](mailto:info@smartlook.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-smartlook-test-user"></a>Criar um usuário de teste do Smartlook

Nesta seção, uma usuária chamado Britta Simon será criada no Smartlook. O Smartlook é compatível com o provisionamento de usuário just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Caso um usuário ainda não exista no Smartlook, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Entrada do Smartlook, na qual será possível iniciar o fluxo de logon.  

* Acesse a URL de Entrada do Smartlook diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado de modo automático ao Smartlook para o qual configurou o SSO 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Smartlook em Meus Aplicativos, caso ele esteja configurado no modo SP, você será redirecionado à página de entrada do aplicativo para iniciar o fluxo de logon. Caso ele esteja configurado no modo IDP, você será conectado de modo automático ao Smartlook para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Smartlook, será possível impor um controle de sessão que fornecerá proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
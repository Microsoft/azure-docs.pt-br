---
title: 'Tutorial: integração do SSO (logon único) do Azure Active Directory com o Panorays | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Panorays.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: jeedes
ms.openlocfilehash: b7a1dc99c81ac5268f191754292ad8f10e793bd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181774"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-panorays"></a>Tutorial: integração do SSO (logon único) do Azure Active Directory com o Panorays

Neste tutorial, você aprenderá a integrar o Panorays ao Azure AD (Azure Active Directory). Quando você integrar o Panorays ao Azure AD, será possível:

* Controlar quem tem acesso ao Panorays no Azure AD.
* Permitir que usuários sejam conectados de modo automático ao Panorays usando contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Panorays.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Panorays é compatível com SSO iniciado por **SP e IDP**
* O Panorays é compatível com o provisionamento de usuário do **Just-In-Time**

## <a name="adding-panorays-from-the-gallery"></a>Como adicionar o Panorays por meio da galeria

Para configurar a integração do Panorays com o Azure AD, será necessário adicionar à sua lista de aplicativos SaaS gerenciados o Panorays da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Panorays** na caixa de pesquisa.
1. Selecione **Panorays** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-panorays"></a>Configurar e testar o SSO do Azure AD para o Panorays

Configure e teste o SSO do Azure AD usando o Panorays com um usuário de teste chamado **B.Simon**. É necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente no Panorays para que o SSO funcione.

Execute as seguintes etapas para configurar e testar o SSO do Azure AD com o Panorays:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Panorays](#configure-panorays-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Panorays](#create-panorays-test-user)** – para ter um equivalente de B.Simon no Panorays que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Panorays**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão Salvar.

1. O aplicativo Panorays espera que as declarações SAML estejam em um formato específico. Isso requer que você adicione um mapeamento de atributos personalizado à sua configuração de atributos do token SAML. A captura de tela a seguir mostrará a lista de atributos padrão, enquanto o **Identificador Exclusivo de Usuário** é mapeado com **user.userprincipalname**. O aplicativo Panorays espera que o **Identificador Exclusivo de Usuário** seja mapeado com o **user.mail**, portanto, será necessário editar e alterar o mapeamento de atributos clicando no ícone **Editar**.

    ![image](common/default-attributes.png)

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

Nesta seção, você permitirá que B.Simon use o logon único do Azure, concedendo acesso ao Panorays.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Panorays**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-panorays-sso"></a>Configurar o SSO do Panorays

Para configurar um logon único no lado do **Panorays**, será necessário enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do Panorays](mailto:support@panorays.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-panorays-test-user"></a>Criar um usuário de teste do Panorays

Nesta seção, uma usuária chamada Britta Simon será criada no Panorays. O Panorays é compatível com o provisionamento de usuário do just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um usuário será criado após a autenticação, caso ele ainda não exista no Panorays.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

Acesse a [URL de Logon do Panorays](https://www.panoraysapp.com) diretamente e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado de modo automático ao Panorays para o qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco Panorays do Painel de Acesso, caso o modo SP esteja configurado, você será redirecionado para a página de entrada do aplicativo a fim de iniciar o fluxo de logon. Caso o modo IDP esteja configurado, você será conectado de modo automático ao Panorays para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o Panorays, será possível impor o controle de sessão, que protegerá contra a exfiltração e a infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
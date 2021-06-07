---
title: 'Tutorial: Integração do Azure Active Directory com a Autenticação SAML do RStudio Connect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a Autenticação SAML do RStudio Connect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182383"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Tutorial: Integração do Azure Active Directory com a Autenticação SAML do RStudio Connect

Neste tutorial, você aprenderá a integrar a Autenticação SAML do RStudio Connect ao Azure AD (Azure Active Directory).
A integração da Autenticação SAML do RStudio Connect ao Azure AD oferece os seguintes benefícios:

* É possível controlar no Azure AD quem terá acesso à Autenticação SAML do RStudio Connect.
* Permitir que os usuários sejam conectados automaticamente à Autenticação SAML do RStudio Connect (logon único) com as contas do Azure AD deles.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a Autenticação SAML do RStudio Connect, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Autenticação SAML do RStudio Connect. Existe uma [avaliação gratuita de 45 dias.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A Autenticação SAML do RStudio Connect é compatível com SSO iniciado por **SP e IDP**

* A Autenticação SAML do RStudio Connect é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>Como adicionar a Autenticação SAML do RStudio Connect por meio da galeria

Para configurar a integração da Autenticação SAML do RStudio Connect com o Azure AD, você precisa adicionar a Autenticação SAML do RStudio Connect da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Autenticação SAML do RStudio Connect** na caixa de pesquisa.
1. Selecione **Autenticação SAML do RStudio Connect** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Configurar e testar o SSO do Azure AD para a Autenticação SAML do RStudio Connect

Configure e teste o SSO do Azure AD com a Autenticação SAML do RStudio Connect usando uma usuária de teste chamada **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na Autenticação SAML do RStudio Connect.

Para configurar e testar o SSO do Azure AD com a Autenticação SAML do RStudio Connect, realize as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
2. **[Configure o SSO de Autenticação SAML do RStudio Connect](#configure-rstudio-connect-saml-authentication-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar de um usuário de teste da Autenticação SAML do RStudio Connect](#create-rstudio-connect-saml-authentication-test-user)** – para ter um equivalente de Brenda Fernandes no RStudio Connect que esteja vinculado à representação da usuária no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos da **Autenticação SAML do RStudio Connect**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas, substituindo `<example.com>` pela porta e endereço do servidor da Autenticação SAML do RStudio Connect:

    ![Informações de logon único de Domínio e URLs da Autenticação SAML do RStudio Connect](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<example.com>/__login__/saml`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<example.com>/__login__/saml/acs`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Upload de metadados da Autenticação SAML do RStudio Connect](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<example.com>/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Eles são determinados com base no endereço do servidor da Autenticação SAML do RStudio Connect (`https://example.com` nos exemplos acima). Entre em contato com a [equipe de suporte da Autenticação SAML do RStudio Connect](mailto:support@rstudio.com) se você tiver problemas. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Seu aplicativo Autenticação SAML do RStudio Connect espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo Autenticação SAML do RStudio Connect espera que **nameidentifier** seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso à Autenticação SAML do RStudio Connect.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Autenticação SAML do RStudio Connect**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>Configurar o SSO da Autenticação SAML do RStudio Connect

Para configurar o logon único para a **Autenticação SAML do RStudio Connect**, você precisa usar a **URL de metadados da Federação de Aplicativos** e o **Endereço do Servidor** usado acima. Isso é feito no arquivo de configuração da Autenticação SAML do RStudio Connect em `/etc/rstudio-connect.rstudio-connect.gcfg`.

Esse é um arquivo de configuração de exemplo:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Armazene o **Endereço do Servidor** no valor `Server.Address` e o **Url de Metadados da Federação de Aplicativos** no valor `SAML.IdPMetaData`. Observe que essa configuração de exemplo usa uma conexão HTTP não criptografada, enquanto o Azure AD requer o uso de uma conexão HTTPS criptografada. Você pode usar um [proxy reverso](https://docs.rstudio.com/connect/admin/proxy/) na frente da Autenticação SAML do RStudio Connect ou configurar a Autenticação SAML do RStudio Connect para [usar HTTPS diretamente](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Se você tiver problemas com a configuração, pode ler o [Guia do Administrador da Autenticação SAML do RStudio Connect](https://docs.rstudio.com/connect/admin/authentication/saml/) ou enviar um email para a [equipe de suporte do RStudio](mailto:support@rstudio.com) para obter ajuda.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>Criar um usuário de teste da Autenticação SAML do RStudio Connect

Nesta seção, você criará uma usuária chamada Brenda Fernandes na Autenticação SAML do RStudio Connect. A Autenticação SAML do RStudio Connect é compatível com o provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se nenhum usuário existir na Autenticação SAML do RStudio Connect, um será criado quando você tentar acessar a Autenticação SAML do RStudio Connect.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon da Autenticação SAML do RStudio Connect, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon da Autenticação SAML do RStudio Connect, na qual você iniciará o fluxo de logon.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente à Autenticação SAML do RStudio Connect para a qual configurou o SSO 

Use também o Painel de Acesso da Microsoft para testar o aplicativo em qualquer modo. Quando você clicar no bloco Autenticação SAML do RStudio Connect no Painel de Acesso, se ele estiver configurado no modo SP, você será redirecionado para a página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IDP, você será conectado automaticamente à Autenticação SAML do RStudio Connect para a qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar a Autenticação SAML do RStudio Connect, você poderá impor um controle de sessão, que fornece proteção contra o vazamento e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
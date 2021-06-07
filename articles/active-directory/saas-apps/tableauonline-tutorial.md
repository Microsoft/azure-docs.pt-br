---
title: 'Tutorial: Integração do Azure Active Directory com o Tableau Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Tableau Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 5318570ed77e3352f37c2306ecd003195992d010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731960"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Tableau Online

Neste tutorial, você aprenderá a integrar o Tableau Online ao Azure AD (Azure Active Directory). Ao integrar o Tableau Online ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Tableau Online.
* Permitir que os usuários sejam conectados automaticamente ao Tableau Online com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Tableau Online.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Tableau Online é compatível com o SSO iniciado por **SP**

## <a name="adding-tableau-online-from-the-gallery"></a>Adição do Tableau Online da galeria

Para configurar a integração do Tableau Online ao Azure AD, você precisa adicionar o Tableau Online da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Tableau Online** na caixa de pesquisa.
1. Selecione **Tableau Online** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Configurar e testar o SSO do Azure AD para o Tableau Online

Nesta seção, você configurará e testará o logon único do Azure AD com o Tableau Online baseado em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tableau Online.

Para configurar e testar o SSO do Azure AD com o Tableau Online, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Tableau Online](#configure-tableau-online-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Tableau Online](#create-tableau-online-test-user)** : para ter um equivalente de B.Fernandes no Tableau Online que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Tableau Online**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Você obterá o valor `<entityid>` na seção **Configurar o Tableau Online** neste tutorial. O valor de ID de entidade será o valor **Identificador do Azure AD** na seção **Configurar o Tableau Online**.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Tableau Online**, copie a URL apropriada, de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Tableau Online.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Tableau Online**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-tableau-online-sso"></a>Configurar o SSO do Tableau Online

1. Para automatizar a configuração no Tableau Online, você precisa instalar a **extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Tableau Online** e você será direcionado ao aplicativo do Tableau Online. Lá, forneça as credenciais de administrador para entrar no Tableau Online. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Tableau Online manualmente, em outra janela do navegador da Web, entre no site da empresa do Tableau Online como administrador.

1. Vá para **Settings** e para **Authentication**.

    ![A captura de tela mostra a opção Autenticação selecionada no menu Configurações.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Para habilitar o SAML, na seção **Tipos de Autenticação**. Marque **Habilitar um método de autenticação adicional** e também a caixa de seleção **SAML**.

    ![A captura de tela mostra a seção Tipos de autenticação, em que você pode selecionar os valores.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Role para baixo até a seção **Importar arquivo de metadados para o Tableau Online**.  Clique em Procurar e importe o arquivo de metadados, que você baixou do Azure AD. Em seguida, clique em **Apply**.

   ![A captura de tela mostra a seção em que você pode importar o arquivo de metadados.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Na seção **Corresponder Instruções de Declaração**, insira o nome de instrução de declaração do Provedor de Identidade correspondente para **endereço de email**, **nome** e **sobrenome**. Para obter essas informações do Azure AD: 
  
    a. No portal do Azure, acesse a página de integração de aplicativos do **Tableau Online**.

    b. Na seção **Atributos e Declarações de Usuário**, clique no ícone Editar.

   ![A captura de tela mostra a seção Atributos e Declarações do Usuário, em que você pode selecionar o ícone de edição.](./media/tableauonline-tutorial/attributesection.png)

    c. Copie o valor de namespace para esses atributos: nome, email e sobrenome usando as seguintes etapas:

   ![A captura de tela mostra os atributos Givenname, Surname e Emailaddress.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Clique no valor **user.givenname**

    e. Copie o valor da caixa de texto **Namespace**.

    ![A captura de tela mostra a seção Gerenciar declarações do usuário, em que você pode inserir o Namespace.](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar os valores de namespace para o email e o sobrenome, repita as etapas acima.

    g. Alterne para o aplicativo Tableau Online e defina a seção **Atributos do usuário e declarações** da seguinte maneira:

    * Email: **mail** ou **userprincipalname**

    * Nome: **givenname**

    * Sobrenome: **surname**

    ![A captura de tela mostra a seção Corresponder atributos, em que você pode inserir os valores.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Criar usuário de teste do Tableau Online

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Tableau Online.

1. No **Tableau Online**, clique em **Configurações** e na seção **Autenticação**. Role para baixo até a seção **Gerenciar Usuários**. Clique em **Adicionar Usuários** e, em seguida, clique em **Inserir Endereços de Email**.
  
    ![A captura de tela mostra a seção Gerenciar usuários, em que você pode selecionar Adicionar usuários.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selecione **Adicionar usuários para autenticação (de SAML)** . Na caixa de texto **Inserir endereços de email**, adicione brenda.fernandes\@contoso.com
  
    ![A captura de tela mostra a página Adicionar Usuários, na qual você pode inserir um endereço de email.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Clique em **Adicionar Usuários**.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Tableau Online, na qual poderá iniciar o fluxo de logon.

* Acesse a URL de Logon do Tableau Online diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Tableau Online em Meus Aplicativos, isso redirecionará você à URL de logon do Tableau Online. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Tableau Online, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
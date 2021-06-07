---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao People | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o People.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 43edf62a625d80de0a5de8e0924e771d68a595e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647079"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o People

Neste tutorial, você aprenderá como integrar o People ao Azure AD (Azure Active Directory). Ao integrar o People ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao People.
* Permitir que os usuários entrem automaticamente no People com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do People habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O People dá suporte ao SSO iniciado por **SP**
* Agora, o aplicativo móvel do People pode ser configurado com o Azure AD para habilitar o SSO. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

>[!NOTE]
>O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-people-from-the-gallery"></a>Adicionar o People da galeria

Para configurar a integração do People ao Azure AD, você precisará adicionar o People à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **People** na caixa de pesquisa.
1. Selecione **People** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-people"></a>Configurar e testar o SSO do Azure AD para o People

Configure e teste o SSO do Azure AD com o People usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do People.

Para configurar e testar o SSO do Azure AD com o People, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do People](#configure-people-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do People](#create-people-test-user)** – para ter um equivalente de B.Fernandes no People que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **People**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<company name>.peoplehr.net`

    b. Na caixa **Identificador**, digite a URL: `https://www.peoplehr.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do People](mailto:customerservices@peoplehr.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o People**, copie a(s) URL(s) apropriada(s) com base em seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao People.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **People**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-people-sso"></a>Configurar o SSO do People

1. Para automatizar a configuração no People, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Instalação do People**. Você será direcionado ao aplicativo People. De lá, forneça as credenciais de administrador para entrar no People. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o People manualmente, abra uma nova janela do navegador da Web, entre no site da empresa People como administrador e execute as seguintes etapas:
   
4. No menu à esquerda, clique em **Configurações**.

    ![Uma captura de tela que mostra o menu do lado esquerdo com a opção "Configurações" selecionada.](./media/people-tutorial/settings.png)

5. Clique em **Empresa**.

    ![Uma captura de tela que mostra a opção "Empresa" selecionada no menu "Configurações".](./media/people-tutorial/company.png)

6. Em **Carregar arquivo de metadados SAML de ‘Logon Único’** , clique em **Procurar** para carregar o arquivo de metadados baixado.

    ![Configurar o logon único](./media/people-tutorial/xml.png)

### <a name="create-people-test-user"></a>Criar usuário de teste do People

Nesta seção, você criará um usuário chamado B.Fernandes no People. Trabalhe com a [equipe de suporte ao cliente do People](mailto:customerservices@peoplehr.com) para adicionar os usuários na plataforma do People. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do People, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do People e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do People em Meus Aplicativos, isso vai redirecionar você à URL de Logon do People. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-people-mobile"></a>Testar o SSO para o People (móvel)

1. Abrir aplicativo móvel do People. Na página de entrada, insira a **ID de email** e, em seguida, clique em **logon único**.

    ![A entrada](./media/people-tutorial/test01.png)

2. Insira a **ID de usuário da organização** e clique em **Avançar**.

    ![O email](./media/people-tutorial/test02.png)

3. Por fim, após entrar com sucesso, a home page do aplicativo será exibida conforme mostrado abaixo:

    ![A uma vez](./media/people-tutorial/test03.png)

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o People, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do Azure Active Directory ao Lucidchart | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Lucidchart.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5d5b07e761d5ed38cb2083054708265189bdd72f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651561"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Lucidchart

Neste tutorial, você aprenderá como integrar o Lucidchart ao Azure AD (Azure Active Directory). Ao integrar o Lucidchart ao Azure AD, você poderá:

* Controlar quem tem acesso ao Lucidchart no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Lucidchart com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Lucidchart.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Lucidchart dá suporte ao SSO iniciado por **SP**
* O Lucidchart dá suporte ao provisionamento de usuário **just-in-time**

## <a name="add-lucidchart-from-the-gallery"></a>Adicionar o Lucidchart por meio da galeria

Para configurar a integração do Lucidchart ao Azure AD, você precisará adicionar o Lucidchart da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Lucidchart** na caixa de pesquisa.
1. Selecione **Lucidchart** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Configurar e testar o SSO do Azure AD para o Lucidchart

Configure e teste o SSO do Azure AD com o Lucidchart usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Lucidchart.

Para configurar e testar o SSO do Azure AD com o Lucidchart, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Lucidchart](#configure-lucidchart-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Lucidchart](#create-lucidchart-test-user)** – para ter um equivalente de B.Fernandes no Lucidchart que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Lucidchart**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

   Na caixa de texto **URL de Logon**, digite a URL como: `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Lucidchart**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Lucidchart.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Lucidchart**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name=&quot;configure-lucidchart-sso&quot;></a>Configurar o SSO do Lucidchart

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Lucidchart como administrador.

2. No menu da parte superior, clique em **Equipe**.

    ![Equipe](./media/lucidchart-tutorial/ic791190.png &quot;Equipe")

3. Clique em **Aplicativos \> Gerenciar SAML**.

    ![Gerenciar SAML](./media/lucidchart-tutorial/ic791191.png "Gerenciar SAML")

4. Na página da caixa de diálogo **Configurações de Autenticação SAML**, siga estas etapas:

    a. Selecione **Ativar Autenticação SAML** e clique em **Opcional**.

    ![Configurações de Autenticação SAML](./media/lucidchart-tutorial/ic791192.png "Configurações de Autenticação SAML")

    b. Na caixa de texto **Domínio**, digite o seu domínio e clique em **Alterar Certificado**.

    ![Alterar Certificado](./media/lucidchart-tutorial/ic791193.png "Alterar Certificado")

    c. Abra o arquivo de metadados baixado, copie o conteúdo e cole-o na caixa de texto **Carregar Metadados**.

    ![Carregar Metadados](./media/lucidchart-tutorial/ic791194.png "Carregar Metadados")

    d. Selecione **Adicionar Automaticamente Novos Usuários à Equipe** e clique em **Salvar Alterações**.

    ![Salvar alterações](./media/lucidchart-tutorial/ic791195.png "Salvar alterações")

### <a name="create-lucidchart-test-user"></a>Criar um usuário de teste do Lucidchart

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Lucidchart.  Quando um usuário atribuído tenta fazer logon no Lucidchart usando o painel de acesso, o Lucidchart verifica se o usuário existe.  

Se não houver conta de usuário ainda, ela é criada automaticamente pelo Lucidchart.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Lucidchart, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Lucidchart e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Lucidchart em Meus Aplicativos, você será conectado automaticamente ao Lucidchart, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

 Após configurar o Lucidchart, você pode impor controles de sessão, que protegem o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
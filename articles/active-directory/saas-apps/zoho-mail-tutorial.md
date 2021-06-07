---
title: 'Tutorial: Integração do Azure Active Directory com Zoho | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: c5778f39a5091753a1658ec121379a4ed29a7542
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648364"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Tutorial: Integração do Azure Active Directory com Zoho

Neste tutorial, você aprenderá a integrar o Zoho ao Azure AD (Azure Active Directory). Ao integrar o Zoho ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Zoho.
* Permitir que os usuários sejam conectados automaticamente ao Zoho com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Zoho One, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único do Zoho.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zoho dá suporte ao SSO iniciado por **SP**

## <a name="add-zoho-from-the-gallery"></a>Adicionar o Zoho da galeria

Para configurar a integração do Zoho com o Azure AD, você precisará adicionar o Zoho à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Zoho** na caixa de pesquisa.
1. Selecione **Zoho** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Configurar e testar o SSO do Azure AD para o Zoho

Configure e teste o SSO do Azure AD com o Zoho usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zoho.

Para configurar e testar o SSO do Azure AD com o Zoho, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Zoho](#configure-zoho-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zoho](#create-zoho-test-user)** – para ter um equivalente de B. Fernandes no Zoho que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Zoho**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<company name>.zohomail.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do Zoho](https://www.zoho.com/mail/contact.html) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Zoho**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso ao Zoho.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Zoho**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name=&quot;configure-zoho-sso&quot;></a>Configurar o SSO do Zoho

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zoho Mail como administrador.

2. Vá para o **Painel de Controle**.
   
    ![Painel de Controle](./media/zoho-mail-tutorial/control-panel.png &quot;Painel de Controle")

3. Clique na guia **Autenticação SAML** .
   
    ![Autenticação SAML](./media/zoho-mail-tutorial/saml-authentication.png "Autenticação do SAML")

4. Na seção **Detalhes da Autenticação SAML** , realize as seguintes etapas:
   
    ![Detalhes da Autenticação SAML](./media/zoho-mail-tutorial/details.png "Detalhes da Autenticação SAML")
   
    a. Na caixa de texto **URL de Logon**, cole a **URL de Logon** copiada no portal do Azure.
   
    b. Na caixa de texto **URL de Logoff**, cole a **URL de Logoff** copiada no portal do Azure.
   
    c. Na caixa de texto **Alterar URL de Senha**, cole **Alterar URL da Senha** copiada do portal do Azure.
       
    d. Abra o certificado codificado em BASE-64 baixado no portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **PublicKey**.
   
    e. Para **Algoritmo**, selecione **RSA**.
   
    f. Clique em **OK**.

### <a name="create-zoho-test-user"></a>Criar um usuário de teste do Zoho

Para permitir que os usuários do AD do Azure façam logon no Zoho Mail, eles devem ser provisionados no Zoho Mail. No caso do Zoho Mail, o provisionamento é uma tarefa manual.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zoho Mail ou APIs fornecidas pelo Zoho Mail para provisionar as contas de usuário do Azure AD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa do **Zoho Mail** como administrador.

1. Vá para **Painel de Controle \> Emails e Documentos**.

1. Vá para **Detalhes do Usuário \> Adicionar Usuário**.
   
    ![A captura de tela mostra o site do Zoho Mail com as opções Detalhes do Usuário e Adicionar Usuário selecionadas.](./media/zoho-mail-tutorial/add-user-1.png "Adicionar usuário")

1. No diálogo **Adicionar usuários** , realize as seguintes etapas:
   
    ![Captura de tela que mostra a caixa de diálogo Adicionar usuários, na qual você pode inserir os valores descritos.](./media/zoho-mail-tutorial/add-user-2.png "Adicionar usuário")
   
    a. Na caixa de texto **Nome**, digite o Nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o Sobrenome do usuário, como **Fernandes**.

    c. Na caixa de texto **ID do Email**, digite a ID do email do usuário como **brendafernandes\@contoso.com**.

    d. Na caixa de texto **Senha** digite a senha do usuário.
   
    e. Clique em **OK**.  
      
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa.

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Zoho, na qual você poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Zoho e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Zoho nos Meus Aplicativos, você deverá ser conectado automaticamente ao Zoho, para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Zoho, você poderá impor um controle de sessão, que fornece proteção contra a exfiltração e a infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
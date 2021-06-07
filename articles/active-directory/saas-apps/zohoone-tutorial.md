---
title: 'Tutorial: Integração do Active Directory do Azure ao Zoho One | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zoho One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 12ac4d9fbf30873f0392a6d767d7568129bad112
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650633"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Integração do Active Directory do Azure ao Zoho One

Neste tutorial, você aprenderá como integrar o Zoho One ao Azure AD (Azure Active Directory). Ao integrar o Zoho One ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Zoho One.
* Permitir que os usuários sejam conectados automaticamente ao Zoho One com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Zoho One, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Zoho One habilitada para logon único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zoho One é compatível com SSO iniciado por **SP** e **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-zoho-one-from-the-gallery"></a>Adicionar o Zoho One da galeria

Para configurar a integração do Zoho One ao Azure AD, você precisa adicionar o Zoho One da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Zoho One** na caixa de pesquisa.
1. Escolha **Zoho One** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Configurar e testar o SSO do Azure AD para o Zoho One

Configure e teste o SSO do Azure AD com o Zoho One usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zoho One.

Para configurar e testar o SSO do Azure AD com o Zoho One, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar SSO do Zoho One](#configure-zoho-one-sso)** – para configurar as definições de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zoho One](#create-zoho-one-test-user)** – para ter um equivalente de B. Fernandes no Zoho One que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Zoho One**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    a. Na caixa de texto **Identificador**, digite a URL `one.zoho.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > O valor **URL de Resposta** anterior não é real. Você obterá o valor `<saml-identifier>` da etapa 4 da seção **Configurar o Logon Único do Zoho One**, que será explicada mais adiante no tutorial.

    c. Clique em **Definir URLs adicionais**.

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `https://one.zoho.com`

5. Se quiser configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > O valor **URL de Entrada** anterior não é real. Você atualizará o valor com a URL de Entrada real da seção **Configurar Logon Único do Zoho One**, explicado posteriormente no tutorial. 

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Zoho One**, copie a URL apropriada de acordo com seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Zoho One.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Zoho One**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-zoho-one-sso"></a>Configurar o SSO do Zoho One

1. Em uma janela diferente do navegador da Web, entre no site da sua empresa Zoho One como administrador.

2. Na guia **Organização**, clique em **Configuração** em **Autenticação SAML**.

    ![Org. Zoho One](./media/zoho-one-tutorial/set-up.png)

3. Na página Pop-up, execute as seguintes etapas:

    ![Assinatura Zoho One](./media/zoho-one-tutorial/save.png)

    a. Na caixa de texto **URL de Conexão**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Na caixa de texto **URL de Saída**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    c. Clique em **Procurar** para carregar o **Certificado (Base64)** que você baixou do portal do Azure.

    d. Clique em **Save** (Salvar).

4. Após salvar a configuração de Autenticação SAML, copie o valor **SAML-Identifier** e acrescente-o à **URL de Resposta** no lugar de `<saml-identifier>`, como `https://accounts.zoho.com/samlresponse/one.zoho.com`, e cole o valor gerado na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML**.

    ![SAML do Zoho One](./media/zoho-one-tutorial/saml-identifier.png)

5. Acesse a guia **Domínios** e, em seguida, clique em **Adicionar Domínio**.

    ![Domínio do Zoho One](./media/zoho-one-tutorial/add-domain.png)

6. Na página **Adicionar Domínio**, execute as seguintes etapas:

    ![Adicionar domínio do Zoho One](./media/zoho-one-tutorial/add-domain-name.png)

    a. Na caixa de texto **Nome de Domínio**, digite um domínio como contoso.com.

    b. Clique em **Adicionar**.

    >[!Note]
    >Depois de adicionar o domínio, siga [estas](https://www.zoho.com/one/help/admin-guide/domain-verification.html) etapas para verificar o domínio. Depois que o domínio for verificado, use o nome do domínio na **URL de Entrada** na seção **Configuração Básica de SAML** no portal do Azure.

### <a name="create-zoho-one-test-user"></a>Criar usuário de teste do Zoho One

Para permitir que os usuários do Azure AD entrem no Zoho One, eles devem ser provisionados no Zoho One. No Zoho One, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Zoho One como Administrador de Segurança.

2. Na guia **Usuários**, clique em **logotipo do usuário**.

    ![Usuário do Zoho One](./media/zoho-one-tutorial/user.png)

3. Na página **Adicionar Usuário**, realize as seguintes etapas:

    ![Adicionar usuário do Zoho One](./media/zoho-one-tutorial/add-user.png)
    
    a. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda Fernandes**.
    
    b. Na caixa de texto **Endereço de Email**, insira o email do usuário como brittasimon@contoso.com.

    >[!Note]
    >Selecione o domínio verificado na lista de domínios.

    c. Clique em **Adicionar**.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

#### <a name="sp-initiated"></a>Iniciado por SP:

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Zoho One, na qual você poderá iniciar o fluxo de logon.  

* Acesse diretamente a URL de Logon do Zoho One e inicie o fluxo de logon nela.

#### <a name="idp-initiated"></a>Iniciado por IdP:

* Clique em **Testar este aplicativo** no portal do Azure e você será conectado automaticamente ao Zoho One, para o qual configurou o SSO. 

Use também os Meus Aplicativos da Microsoft para testar o aplicativo em qualquer modo. Ao clicar no bloco do Zoho One em Meus Aplicativos, se ele estiver configurado no modo SP, você será redirecionado à página de logon do aplicativo para iniciar o fluxo de logon e, se ele estiver configurado no modo IdP, você será conectado automaticamente ao Zoho One, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Zoho One, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do SSO do Azure Active Directory ao Cloud Academy – SSO'
description: Neste tutorial, você aprenderá a configurar o logon único entre o Azure Active Directory e o Cloud Academy – SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: fa46d6e5c7f1007e3a90e22eb9d4f46e18251a28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729804"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Tutorial: Integração do logon único do Azure Active Directory ao Cloud Academy – SSO

Neste tutorial, você aprenderá a integrar o Cloud Academy – SSO ao Azure Active Directory (Azure AD). Com a integração do Cloud Academy – SSO ao Azure AD, você poderá:

* Usar o Azure AD para controlar quem pode acessar o Cloud Academy – SSO.
* Permitir que os usuários, usando as respectivas contas do Azure AD, sejam conectados automaticamente ao Cloud Academy – SSO.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Cloud Academy – SSO com SSO (logon único) habilitado.

## <a name="tutorial-description"></a>Descrição do tutorial

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cloud Academy – SSO é compatível com SSO iniciado por **SP**
* O Cloud Academy – SSO é compatível com provisionamento de usuário **Just-In-Time**

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Adicionar o Cloud Academy – SSO por meio da galeria

Para configurar a integração do Cloud Academy – SSO ao Azure AD, você precisa adicionar o Cloud Academy – SSO por meio da galeria à sua lista de aplicativos SaaS gerenciados:

1. Entre no portal do Azure com uma conta corporativa ou de estudante ou com uma conta Microsoft pessoal.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Acesse **Aplicativos empresariais** e, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, insira **Cloud Academy – SSO** na caixa de pesquisa.
1. Selecione **Cloud Academy – SSO** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Configurar e testar o SSO do Azure AD para o Cloud Academy – SSO

Você vai configurar e testar o SSO do Azure AD com o Cloud Academy – SSO usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente do Cloud Academy – SSO.

Para configurar e testar o SSO do Azure AD com o Cloud Academy – SSO, conclua estas etapas de alto nível:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
    1. **[Permita acesso ao usuário de teste](#grant-access-to-the-test-user)** para permitir que o usuário use o logon único do Azure AD.
1. **[Configure o logon único para o Cloud Academy – SSO](#configure-single-sign-on-for-cloud-academy)** no lado do aplicativo.
    1. **[Crie um usuário de teste do Cloud Academy – SSO](#create-a-cloud-academy-test-user)** como um equivalente à representação do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. No portal do Azure, na página de integração de aplicativos do **Cloud Academy – SSO**, na seção **Gerenciar**, selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o botão de lápis da **Configuração Básica de SAML** para editar as configurações:

   ![Captura de tela que mostra o ícone de lápis para edição da configuração básica de SAML.](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma das seguintes URLs:
    
    | URL de logon |
    |--------------|
    | `https://cloudacademy.com/login/enterprise/` |
    | `https://app.qa.com/login/enterprise/` |
    |
    
    b. Na caixa de texto **URL de Resposta**, digite uma das seguintes URLs:
    
    | URL de resposta |
    |--------------|
    | `https://cloudacademy.com/labs/social/complete/saml/` |
    | `https://app.qa.com/labs/social/complete/saml/` |
    |
1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o botão copiar para copiar a **URL de Metadados de Federação de Aplicativos**. Salve a URL.

    ![Captura de tela que mostra o botão copiar para a URL de metadados de federação de aplicativos.](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory**. Selecione **Usuários** e, em seguida, selecione **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, conclua estas etapas:
   1. Na caixa **Nome**, insira **B.Fernandes**.  
   1. Na caixa **Nome de usuário**, insira \<username>@\<companydomain>.\<extension>. Por exemplo, `B.Simon@contoso.com`.
   1. Marque **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="grant-access-to-the-test-user"></a>Permitir acesso ao usuário de teste

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a esse usuário acesso ao Cloud Academy – SSO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Cloud Academy – SSO**.
1. Na página de visão geral do aplicativo, na seção **Gerenciar**, selecione **Usuários e grupos**:
1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**:
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de **Usuários** e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Configurar o logon único para o Cloud Academy

1. Em outra janela do navegador, entre no seu site de empresa do Cloud Academy – SSO como administrador.

1. Selecione o nome da sua empresa e, em seguida, selecione **Configurações e Integrações** no menu que aparece:

    ![Captura de tela que mostra a opção Configurações e Integrações.](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Na página **Configurações e Integrações**, na guia **Integrações**, selecione o cartão **SSO**:

    ![Captura de tela que mostra o cartão SSO na guia Integrações.](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Conclua as seguintes etapas nesta página:

    ![Captura de tela que mostra a página Integrações > SSO.](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Na caixa **URL da ID da Entidade**, insira o valor da ID da entidade que você copiou do portal do Azure.

    b. Na caixa **URL de SSO**, cole o valor da URL de logon copiado do portal do Azure.

    c. Abra o certificado Base64 baixado do portal do Azure no Bloco de Notas. Cole o conteúdo na caixa **Certificado**.

    d. Na caixa **Formato da ID de Nome**, use o valor padrão: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

1. Selecione **Salvar**.

    > [!NOTE]
    > Para obter mais informações sobre como configurar o Cloud Academy – SSO, confira [Configurar o Logon Único](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-a-cloud-academy-test-user"></a>Criar um usuário de teste do Cloud Academy

Nesta seção, um usuário chamado Brenda Fernandes será criado no Cloud Academy – SSO. O Cloud Academy – SSO é compatível com o provisionamento de usuário Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Cloud Academy – SSO, será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Cloud Academy – SSO, na qual poderá iniciar o fluxo de logon. 

* Acesse diretamente a URL de Logon do Cloud Academy – SSO, na qual você iniciará o fluxo de logon.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Cloud Academy – SSO em Meus Aplicativos, você será redirecionado à URL de Logon do Cloud Academy – SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Após configurar o Cloud Academy – SSO, você poderá impor o controle de sessão, que protegerá contra a exfiltração e a infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
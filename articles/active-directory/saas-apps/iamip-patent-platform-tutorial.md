---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao IamIP Patent Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o IamIP Patent Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: dc9be41cd7975610f2779da09b94066c70a91652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460522"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao IamIP Patent Platform

Neste tutorial, você aprenderá a integrar o IamIP Patent Platform ao Azure AD (Azure Active Directory). Ao integrar o IamIP Patent Platform ao Azure AD, você pode:

* Use o Azure AD para controlar quem pode acessar o IamIP Patent Platform.
* Permitir que os usuários sejam conectados automaticamente ao IamIP Patent Platform com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [Logon único em aplicativos no Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do IamIP Patent Platform com SSO (logon único) habilitado.

## <a name="tutorial-description"></a>Descrição do tutorial

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

O IamIP Patent Platform é compatível com SSO iniciado por SP e por IDP.

Depois de configurar o IamIP Patent Platform, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Adicionar o IamIP Patent Platform por meio da galeria

Para configurar a integração do IamIP Patent Platform ao Azure AD, você precisará adicionar o IamIP Patent Platform por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta corporativa ou de estudante ou com uma conta Microsoft pessoal.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Acesse **Aplicativos empresariais** e, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **IamIP Patent Platform** na caixa de pesquisa.
1. Selecione **IamIP Patent Platform** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Configurar e testar o SSO do Azure AD para o IamIP Patent Platform

Você vai configurar e testar o SSO do Azure AD com o IamIP Patent Platform usando um usuário de teste chamado B.Fernandes. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente no IamIP Patent Platform.

Para configurar e testar o SSO do Azure AD com o IamIP Patent Platform, você seguirá estas etapas de alto nível:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD.
    * **[Permita acesso ao usuário de teste](#grant-access-to-the-test-user)** para permitir que o usuário use o logon único do Azure AD.

1. **[Configure o SSO do IamIP Patent Platform](#configure-iamip-patent-platform-sso)** no lado do aplicativo.
    * **[Crie um usuário de teste do IamIP Patent Platform](#create-iamip-patent-platform-test-user)** como um equivalente à declaração do usuário no Azure AD.

1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **IamIP Patent Platform**, na seção **Gerenciar**, selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o botão de lápis da **Configuração Básica de SAML** para editar as configurações:

   ![Botão de lápis para configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, se você tiver um Arquivo de metadados do Provedor de Serviço e quiser configurar o SSO no modo iniciado por IDP, execute estas etapas:

    a. Selecione **Carregar o arquivo de metadados**:

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Selecione o botão da pasta, selecione o arquivo de metadados e selecione **Carregar**:

    ![botões Pasta e Carregar](common/browse-upload-metadata.png)

    c. Após o upload do arquivo de metadados, os valores de **Identificador** e a **URL de Resposta** são preenchidos automaticamente na seção **Configuração Básica do SAML**:

    ![Valores de URL de Resposta e Identificador](common/idp-intiated.png)

    > [!Note]
    > Se os valores de **Identificador** e **URL de Resposta** não forem preenchidos automaticamente, forneça-os manualmente de acordo com suas necessidades.

1. Selecione **Definir URLs adicionais** e conclua a seguinte etapa se desejar configurar o aplicativo no modo iniciado por SP:

    Na caixa **URL de entrada**, insira **https:\//patents.iamip.com/login-user**.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o link **Download** para **Certificado (Bruto)** para baixar o certificado e salvá-lo no computador:

    ![Link de download do certificado](common/certificateraw.png)

1. Na seção **Configurar o IamIP Patent Platform**, copie as URLs apropriadas de acordo com as suas necessidades:

    ![Copiar URLs de configuração](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory**. Selecione **Usuários** e, em seguida, selecione **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, conclua estas etapas:
   1. Na caixa **Nome**, insira **B.Fernandes**.  
   1. Na caixa **Nome de usuário**, insira \<username>@\<companydomain>.\<extension>. Por exemplo, `B.Simon@contoso.com`.
   1. Marque **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="grant-access-to-the-test-user"></a>Permitir acesso ao usuário de teste

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso do usuário ao IamIP Patent Platform.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **IamIP Patent Platform**.
1. Na página de visão geral do aplicativo, na seção **Gerenciar**, selecione **Usuários e grupos**:

   ![Selecionar Usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**:

    ![Selecione Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de **Usuários** e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.

## <a name="configure-iamip-patent-platform-sso"></a>Configurar o SSO do IamIP Patent Platform

Para configurar o logon único no lado do IamIP Patent Platform, é necessário enviar o certificado bruto baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do IamIP Patent Platform](mailto:info@iamip.com). Ela configura a conexão de SSO do SAML para que esteja correta em ambos os lados.

### <a name="create-iamip-patent-platform-test-user"></a>Criar um usuário de teste do IamIP Patent Platform

Trabalhe com a [equipe de suporte do IamIP Patent Platform](mailto:info@iamip.com) para adicionar um usuário chamado B.Fernandes no IamIP Patent Platform. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do IamIP Patent Platform no Painel de Acesso, você deverá ser conectado automaticamente à instância do IamIP Patent Platform para a qual configurou o SSO. Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o IamIP Patent Platform com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
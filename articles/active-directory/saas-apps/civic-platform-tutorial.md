---
title: 'Tutorial: Integração do Azure Active Directory ao Civic Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Civic Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496441"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>Tutorial: Integrar o Civic Platform ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Civic Platform ao Azure AD (Azure Active Directory). Ao integrar o Civic Platform ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Civic Platform.
* Permitir que os usuários sejam conectados automaticamente ao Civic Platform com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura habilitada para SSO (logon único) do Civic Platform.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Civic Platform dá suporte ao SSO iniciado por **SP**





## <a name="adding-civic-platform-from-the-gallery"></a>Adicionando o Civic Platform por meio da galeria

Para configurar a integração do Civic Platform ao Azure AD, você precisa adicionar o Civic Platform à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Civic Platform** na caixa de pesquisa.
1. Selecione **Civic Platform** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Civic Platform usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Civic Platform.

Para configurar e testar o SSO do Azure AD com o Civic Platform, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Civic Platform](#configure-civic-platform-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Civic Platform](#create-civic-platform-test-user)** – para ter um equivalente de Brenda Fernandes no Civic Platform que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Civic Platform**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.accela.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL: `civicplatform.accela.com`

    > [!NOTE]
    > O valor da URL de Entrada não é real. Atualize esse valor com a URL de logon real. Entre em contato com a [equipe de suporte ao Cliente do Civic Platform](mailto:skale@accela.com) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

1. Navegue até **Azure Active Directory** > **Registros de aplicativo** no Azure AD e selecione seu aplicativo.

1. Copie o **Diretório (ID do locatário)** e armazene-o no Bloco de notas.

    ![Copiar o diretório (ID do locatário) e armazene-o no código do aplicativo](media/civic-platform-tutorial/directoryid.png)

1. Copie a **ID do Aplicativo** e armazene-a no Bloco de notas.

   ![Copiar a ID (de cliente) do aplicativo](media/civic-platform-tutorial/applicationid.png)

1. Navegue até **Azure Active Directory** > **Registros de aplicativo** no Azure AD e selecione seu aplicativo. Selecione **Certificados e segredos**.

1. Selecione **Segredos do cliente > Novo segredo do cliente**.

1. Forneça uma descrição do segredo e uma duração. Ao terminar, selecione **Adicionar**.

   > [!NOTE]
   > Depois de salvar o segredo do cliente, o valor do segredo do cliente é exibido. Copie este valor, pois não é possível recuperar a chave posteriormente.

   ![Copiar o valor do segredo porque você não pode recuperá-lo mais tarde](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>Configurar SSO do Civic Platform

1. Abra uma nova janela do navegador da Web e entre no site de empresa do Atlassian Cloud como administrador.

1. Clique em **Opções Padrão**.

    ![O link de download do Certificado](media/civic-platform-tutorial/standard-choices.png)

1. Crie uma opção **ssoconfig** padrão.

1. Pesquise **ssoconfig** e envie.

    ![O link de download do Certificado](media/civic-platform-tutorial/sso-config.png)

1. Expanda SSOCONFIG, clicando no ponto vermelho.

    ![O link de download do Certificado](media/civic-platform-tutorial/sso-config01.png)

1. Forneça informações de configuração relacionadas a SSO na seguinte etapa:

    ![O link de download do Certificado](media/civic-platform-tutorial/sso-config02.png)

    1. No campo **applicationid**, insira o valor da **ID do Aplicativo** que você copiou do portal do Azure.

    1. No campo **clientSecret**, insira o valor do **Segredo** que você copiou do portal do Azure.

    1. No campo **directoryId**, insira o valor do **Diretório (ID do locatário)** que você copiou do portal do Azure.

    1. Insira o idpName. Por exemplo: `Azure`.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Civic Platform.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Civic Platform**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-civic-platform-test-user"></a>Criar um usuário de teste do Civic Platform

Nesta seção, você criará um usuário chamado B.Fernandes no Civic Platform. Trabalhe com a [equipe de suporte ao cliente do Civic Platform](mailto:skale@accela.com) para adicionar os usuários a essa equipe. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Civic Platform no Painel de Acesso, você deverá ser conectado automaticamente ao Civic Platform para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


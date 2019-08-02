---
title: 'Tutorial: Integração do Azure Active Directory com o Kit de Ferramentas SAML do Azure AD | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Kit de Ferramentas SAML do Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baeda4fca69969fae3f866e642ad3dc8ad46509
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091929"
---
# <a name="tutorial-integrate-azure-ad-saml-toolkit-with-azure-active-directory"></a>Tutorial: Integrar o Kit de Ferramentas SAML do Azure AD ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Kit de Ferramentas SAML do Azure AD ao Azure AD (Azure Active Directory). Quando você integra o Kit de Ferramentas SAML do Azure AD com o Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Kit de Ferramentas SAML do Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Kit de Ferramentas SAML do Azure AD com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do Kit de Ferramentas SAML do Azure AD.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Kit de Ferramentas SAML do Azure AD dá suporte a SSO iniciado por **SP**.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Adicionar o Kit de Ferramentas SAML do Azure AD da galeria

Para configurar a integração do Kit de Ferramentas SAML do Azure AD ao Azure AD, é necessário adicionar o Kit de Ferramentas SAML do Azure AD à lista de aplicativos SaaS gerenciados da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Kit de Ferramentas SAML do Azure AD** na caixa de pesquisa.
1. Selecione **Kit de Ferramentas SAML do Azure AD** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Kit de Ferramentas SAML do Azure AD usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kit de Ferramentas SAML do Azure AD.

Para configurar e testar o SSO do Azure AD com o Kit de Ferramentas SAML do Azure AD, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar o Kit de Ferramentas SAML do Azure AD](#configure-azure-ad-saml-toolkit-sso)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Kit de Ferramentas SAML do Azure AD](#create-azure-ad-saml-toolkit-test-user)** para ter um equivalente de B.Fernandes no Kit de Ferramentas SAML do Azure AD que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Kit de Ferramentas SAML do Azure AD**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    1. Na caixa de texto **URL de logon**, digite uma URL: `https://samltoolkit.azurewebsites.net/`

    1. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL: `https://samltoolkit.azurewebsites.net`

    1. Na caixa de texto **URL de Resposta**, insira uma URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

   ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Kit de Ferramentas SAML do Azure AD**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-azure-ad-saml-toolkit-sso"></a>Configurar o SSO do Kit de Ferramentas SAML do Azure AD

1. Abra uma nova janela do navegador da Web, se você não se registrou no site do Kit de Ferramentas do SAML do Azure AD, registre-se primeiro clicando em **Registrar**. Se você já se registrou, entre no site da empresa do Kit de Ferramentas do SAML do Azure AD usando as credenciais de login registradas.

    ![Registro do Kit de Ferramentas do SAML do Azure AD](./media/saml-toolkit-tutorial/register.png)

1. Clique na **Configuração de SAML**.

    ![Configuração de SAML do Kit de Ferramentas SAML do Azure AD](./media/saml-toolkit-tutorial/saml-configure.png)

1. Clique em **Criar**.

    ![SSO de criação do Kit de Ferramentas SAML do Azure AD](./media/saml-toolkit-tutorial/createsso.png)

1. Na página **Configuração do SSO do SAML**, realize as seguintes etapas:

    ![SSO de criação do Kit de Ferramentas SAML do Azure AD](./media/saml-toolkit-tutorial/fill-details.png)

    1. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    1. Na caixa de texto **Identificador do Azure AD**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    1. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiada do portal do Azure.

    1. Clique em **Escolher Arquivo** para carregar o arquivo **XML de Metadados de Federação** que você baixou do portal do Azure.

    1. Clique em **Criar**.

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

Nesta seção, você permitirá que o B.Fernandes use o logon único do Azure, concedendo acesso ao Kit de Ferramentas do SAML do Azure AD.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Kit de Ferramentas do SAML do Azure AD**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Criar usuário de teste do Kit de Ferramentas do SAML do Azure AD

Nesta seção, um usuário chamado B.Fernandes é criado no Kit de Ferramentas do SAML do Azure AD. O Kit de Ferramentas do SAML do Azure AD oferece suporte ao provisionamento de usuário just-in-time, que é ativado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Kit de Ferramentas do SAML do Azure AD, um novo é criado após a autenticação.

### <a name="test-sso"></a>Testar o SSO

Quando você seleciona o bloco Kit de Ferramentas do SAML do Azure AD no Painel de Acesso, você deve entrar automaticamente no Kit de Ferramentas do SAML do Azure AD para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

---
title: 'Tutorial: Integração do Azure Active Directory com a Autenticação SAML da CyberArk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a Autenticação SAML da CyberArk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 58d8a0be-5f1b-4680-bbcb-2975e5c57014
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 612de66bfdc2778a87685e0157ba8ef013ac51b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112908"
---
# <a name="tutorial-integrate-cyberark-saml-authentication-with-azure-active-directory"></a>Tutorial: Integrar a Autenticação SAML da CyberArk ao Azure Active Directory

Neste tutorial, você aprenderá a integrar a Autenticação SAML da CyberArk ao Azure AD (Azure Active Directory). Ao integrar a Autenticação SAML da CyberArk ao Azure AD, é possível:

* Controlar quem tem acesso à Autenticação SAML da CyberArk no Azure AD.
* Permitir que seus usuários entrem automaticamente na Autenticação SAML da CyberArk com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) da Autenticação SAML da CyberArk.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. A Autenticação SAML da CyberArk oferece suporte a SSO iniciado por **SP e IDP**.

## <a name="adding-cyberark-saml-authentication-from-the-gallery"></a>Adicionando a Autenticação SAML da CyberArk da galeria

Para configurar a integração da Autenticação SAML da CyberArk, você precisará adicionar a Autenticação SAML da CyberArk por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Autenticação SAML da CyberArk** na caixa de pesquisa.
1. Selecione **Autenticação SAML da CyberArk** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD na Autenticação SAML da CyberArk usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na Autenticação SAML da CyberArk.

Para configurar e testar o SSO do Azure AD na Autenticação SAML da CyberArk, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configure a Autenticação SAML da CyberArk](#configure-cyberark-saml-authentication)**  para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Crie um usuário de teste da Autenticação SAML da CyberArk](#create-cyberark-saml-authentication-test-user)**  para ter um equivalente de B. Fernandes na Autenticação SAML da CyberArk que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **Autenticação SAML da CyberArk**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute a seguinte etapa:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<PVWA DNS or IP>/passwordvault/api/auth/saml/logon`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<PVWA DNS or IP>/PasswordVault/v10/logon/saml`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente da Autenticação SAML da CyberArk](mailto:bizdevtech@cyberark.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Raw)** e selecione **Baixar** para salvar o certificado no computador.

   ![O link de download do Certificado](common/certificateraw.png)

1. Na seção **Configurar a Autenticação SAML da CyberArk**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-cyberark-saml-authentication"></a>Configurar a Autenticação SAML da CyberArk

Para configurar o logon único no lado da **Autenticação SAML da CyberArk**, você precisará enviar o **Certificado (Raw)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte da Autenticação SAML da CyberArk](mailto:bizdevtech@cyberark.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B. Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B. Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure dando a ela acesso à Autenticação SAML da CyberArk.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Autenticação SAML da CyberArk**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B. Fernandes** na lista Usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-cyberark-saml-authentication-test-user"></a>Criar usuário de teste da Autenticação SAML da CyberArk

Nesta seção, você criará uma usuária de teste na Autenticação SAML da CyberArk chamada Brenda Fernandes. Trabalhe com a  [equipe de suporte da Autenticação SAML da CyberArk](mailto:bizdevtech@cyberark.com) para adicionar os usuários à plataforma da Autenticação SAML da CyberArk. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco da Autenticação SAML da CyberArk no Painel de Acesso, você deve ser conectado automaticamente à Autenticação SAML da CyberArk para a qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 'Tutorial: Integração do Azure Active Directory com a AcquireIO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75a92e78e7293316cad6e567ae49c4998299415c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544557"
---
# <a name="tutorial-integrate-acquireio-with-azure-active-directory"></a>Tutorial: Integrar a AcquireIO ao Azure Active Directory

Neste tutorial, você aprenderá a integrar a AcquireIO ao Azure AD (Azure Active Directory). Ao integrar a AcquireIO ao Azure AD, é possível:

* Controlar quem tem acesso à AcquireIO no Azure AD.
* Permitir que seus usuários entrem automaticamente na AcquireIO com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) da AcquireIO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. A AcquireIO oferece suporte a SSO iniciado por **IDP**.

## <a name="adding-acquireio-from-the-gallery"></a>Adicionando a AcquireIO da galeria

Para configurar a integração da AcquireIO ao Azure AD, você precisará adicionar a AcquireIO por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AcquireIO** na caixa de pesquisa.
1. Selecione **AcquireIO** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD na AcquireIO com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na AcquireIO.

Para configurar e testar o SSO do Azure AD na AcquireIO, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configure a AcquireIO](#configure-acquireio)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Crie um usuário de teste da AcquireIO](#create-acquireio-test-user)** para ter um equivalente de B.Fernandes ali que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **AcquireIO**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute a seguinte etapa:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > O valor não é real. Você receberá a URL de resposta real, que será explicada posteriormente na seção **Configurar a AcquireIO** do tutorial. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar a AcquireIO**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-acquireio"></a>Configurar a AcquireIO

1. Em uma janela diferente do navegador da Web, entre na AcquireIO como administrador.

2. Do lado esquerdo do menu, clique em **App Store**.

     ![Configuração da AcquireIO](./media/acquireio-tutorial/config01.png)

3. Role para baixo até **Active Directory** e clique em **Instalar**.

    ![Configuração da AcquireIO](./media/acquireio-tutorial/config02.png)

4. No pop-up do Active Directory, execute as seguintes etapas:

    ![Configuração da AcquireIO](./media/acquireio-tutorial/config03.png)

    a. Clique para **Copiar** a URL de resposta para sua instância e cole-a na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    b. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra o certificado codificado em Base64 no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado X.509**.

    d. Clique em **Conectar Agora**.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure dando a ela acesso à AcquireIO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AcquireIO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-acquireio-test-user"></a>Criar usuário de teste da AcquireIO

Para permitir que os usuários do Microsoft Azure AD façam logon na AcquireIO, eles devem estar provisionados na AcquireIO. Na AcquireIO, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Em uma janela diferente do navegador da Web, entre na AcquireIO como administrador.

2. No lado esquerdo do menu, clique em **Perfis** e navegue até **Adicionar Perfil**.

     ![Configuração da AcquireIO](./media/acquireio-tutorial/config04.png)

3. No pop-up **Adicionar cliente**, execute as seguintes etapas:

    ![Configuração da AcquireIO](./media/acquireio-tutorial/config05.png)

    a. Na caixa de texto **Nome**, insira o nome do usuário, por exemplo, **B.Fernandes**.

    b. Na caixa de texto **E-mail**, insira o e-mail do usuário como **B.simon@contoso.com** .

    c. Clique em **Enviar**.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco da AcquireIO no Painel de Acesso, você deve ser conectado automaticamente à AcquireIO para a qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
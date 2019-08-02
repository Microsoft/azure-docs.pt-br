---
title: 'Tutorial: Integração do Azure Active Directory ao MyWorkDrive | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 0c3eb72abc90347c8e18a2f56a5d4756ecd80f3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096331"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Tutorial: Integrar o MyWorkDrive com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o MyWorkDrive ao Azure AD (Azure Active Directory). Ao integrar o MyWorkDrive ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao MyWorkDrive.
* Permitir que os usuários sejam conectados automaticamente ao MyWorkDrive com suas contas do Azure AD.
* Gerencie suas contas em uma localização central – o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do MyWorkDrive com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O MyWorkDrive dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-myworkdrive-from-the-gallery"></a>Adicionar o MyWorkDrive da galeria

Para configurar a integração do MyWorkDrive ao Microsoft Azure AD, será necessário adicionar o MyWorkDrive por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **MyWorkDrive** na caixa de pesquisa.
1. Selecione **MyWorkDrive** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o MyWorkDrive usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do MyWorkDrive.

Para configurar e testar o SSO do Azure AD com o MyWorkDrive, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do MyWorkDrive](#configure-myworkdrive-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do MyWorkDrive](#create-myworkdrive-test-user)** – para ter um equivalente de Brenda Fernandes no MyWorkDrive que esteja vinculado à representação de usuário no Microsoft Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **MyWorkDrive**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, selecione **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores do seguinte campo:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Insira o nome do host do MyWorkDrive Server da sua empresa: por exemplo,
    > 
    > URL de Resposta: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de logon:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Entre em contato com a [equipe de suporte do MyWorkDrive](mailto:support@myworkdrive.com) se você não tiver certeza de como configurar seu próprio nome de host e certificado SSL para esses valores.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** para sua área de transferência.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Configurar o SSO do MyWorkDrive

1. Em uma janela de navegador da Web diferente, entre como administrador de segurança do MyWorkDrive.

2. No MyWorkDrive Server no painel de administração, clique em **ENTERPRISE** e execute as seguintes etapas:

    ![O administrador](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Habilitar **SSO de SAML/ADFS**.

    b. Selecione **SAML - Microsoft Azure Active Directory**

    c. Na caixa de texto **Url Metadados da Federação de Aplicativos do Azure**, cole o valor de **URL de Metadados da Federação de Aplicativos** que você copiou do portal do Azure.

    d. Clique em **Salvar**

    > [!NOTE]
    > Para obter informações adicionais, consulte o [artigo de suporte do MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes para usar o logon único do Azure permitindo a ela acesso ao MyWorkDrive.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **MyWorkDrive**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-myworkdrive-test-user"></a>Criar usuário de teste do MyWorkDrive

Nesta seção, você criará uma usuária chamada Brenda Fernandes no MyWorkDrive. Trabalhe com a [equipe de suporte do MyWorkDrive](mailto:support@myworkdrive.com) para adicionar os usuários na plataforma do MyWorkDrive. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO

Ao clicar no bloco do MyWorkDrive no Painel de Acesso, você deverá ser conectado automaticamente ao MyWorkDrive no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 'Tutorial: Integração do Azure Active Directory ao Appraisd | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561191"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Tutorial: Integrar o Appraisd ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Appraisd ao Azure AD (Azure Active Directory). Ao integrar o Appraisd ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Appraisd.
* Permitir que os usuários sejam conectados automaticamente ao Appraisd com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Appraisd.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Appraisd é compatível com o SSO iniciado por **SP e IdP**.

## <a name="adding-appraisd-from-the-gallery"></a>Adicionando o Appraisd por meio da galeria

Para configurar a integração do Appraisd ao Azure Active Directory você precisará adicionar o Appraisd da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Appraisd** na caixa de pesquisa.
1. Selecione **Appraisd** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Appraisd usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Appraisd.

Para configurar e testar o SSO do Azure AD com o Appraisd, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
2. **[Configurar o Appraisd](#configure-appraisd)** para definir as configurações de SSO no lado do aplicativo.
3. **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Appraisd](#create-appraisd-test-user)** para ter um equivalente de B. Fernandes no Appraisd que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Appraisd**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão Salvar e executar as seguintes etapas:

    a. Clique em **Definir URLs adicionais**.

    b. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `<TENANTCODE>`

    c. Se você deseja configurar o aplicativo no modo iniciado pelo **SP**, na caixa de texto **URL de Logon** digite uma URL usando o seguinte padrão: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Você pode obter o valor real de URL de logon e o estado de retransmissão na página de configuração de SSO Appraisd que é explicada posteriormente no tutorial.

1. O aplicativo Appraisd espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo Appraisd espera que **nameidentifier** seja mapeado com **user.mail**. Portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

   ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Appraisd**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Configurar o Appraisd

1. Para automatizar a configuração no Appraisd, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, ao clicar em **Instalar o Appraisd**, você será direcionado ao aplicativo Appraisd. Nele, forneça as credenciais de administrador para entrar no Appraisd. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![Configuração da instalação](common/setup-sso.png)

3. Caso deseje configurar o Appraisd manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do Appraisd como administrador e execute as seguintes etapas:

4. No canto superior direito da página, clique no ícone **Configurações**, em seguida, navegue até **Configuração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Do lado esquerdo do menu, clique em **Logon único SAML**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Na página **Configuração de Logon Único SAML 2.0**, realize as seguintes etapas:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Copiar o **Estado de retransmissão padrão** de valor e cole-o na caixa de texto **Estado de retransmissão** **Configuração SAML Básica** no portal do Azure.

    b. Cópia de **URL de logon iniciado pelo serviço** de valor e cole-o na caixa de texto **URL de logon** **Configuração Básica SAML** no portal do Azure.

7. Role para baixo em sintonia **identificando usuários**, execute as seguintes etapas:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Na caixa de texto **URL do Provedor de identidade de Logon único**, cole o valor da **URL de logon**, que você copiou do portal do Azure e clique em **Salvar**.

    b. Na caixa de texto **URL de Emissor do Provedor de Identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure e clique em **Salvar**.

    c. No bloco de notas, abra o certificado codificado em base 64 que você baixou do portal do Azure, copie seu conteúdo e, em seguida, cole-o na caixa **certificado x. 509**e clique em **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Appraisd.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Appraisd**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B. Fernandes** na lista Usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-appraisd-test-user"></a>Criar um usuário de teste do Appraisd

Para habilitar o Azure Active Directory, os usuários entram no Appraisd, eles devem ser provisionados no Appraisd. No Appraisd, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Appraisd como um Administrador de Segurança.

2. No canto superior direito da página, clique no ícone **Configurações**, em seguida, navegue até **Centro de administração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na barra de ferramentas na parte superior da página, clique em **Pessoas**, em seguida, navegue até **Adicionar um novo usuário**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na página **Adicionar um novo usuário**, execute as seguintes etapas:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **simon**.

    c. Na caixa de texto **Email**, insira o email do usuário como `B. Simon@contoso.com`.

    d. Clique em **Adicionar usuário**.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Appraisd no Painel de Acesso, você deverá ser conectado automaticamente ao Appraisd, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

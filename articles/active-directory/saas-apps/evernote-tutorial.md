---
title: 'Tutorial: Integração do Azure Active Directory ao Evernote | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 4185e9f5e1b411f95afacb80499db048769ea27b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102966"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Tutorial: Integração do Azure Active Directory ao Evernote

Neste tutorial, você aprenderá a integrar o Evernote ao Azure AD (Azure Active Directory).
A integração do Evernote ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Evernote.
* Você pode permitir que os usuários sejam conectados automaticamente ao Evernote (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Evernote, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Evernote

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Evernote dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-evernote-from-the-gallery"></a>Adicionar o Evernote da galeria

Para configurar a integração do Evernote ao Azure AD, você precisará adicionar o Evernote da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Evernote da galeria, execute as seguintes etapas:**

1. No **[portal do Microsoft Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Evernote**, selecione **Evernote** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![Evernote na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Evernote, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Evernote.

Para configurar e testar o logon único do Azure AD com o Evernote, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Evernote](#configure-evernote-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Evernote](#create-evernote-test-user)** – para ter um equivalente de Brenda Fernandes no Evernote que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Evernote, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Evernote**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute a seguinte etapa:

    ![Informações de logon único de URLs e Domínio do Evernote](common/idp-identifier.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://www.evernote.com/saml2`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://www.evernote.com/Login.action`

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Para modificar as opções de **Autenticação**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Selecione a opção **Assinar resposta SAML e declaração** para **Opção de Assinatura**.

    b. Clique em **Salvar**

8. Na seção **Configurar o Evernote**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-evernote-single-sign-on"></a>Configurar o logon único do Evernote

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do Evernote como administrador.

2. Acesse **'Console de Administração'**

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. No **'Console de Administração'** , acesse **'Segurança'** e selecione **'Logon Único'**

    ![SSO-Setting](./media/evernote-tutorial/tutorial_evernote_sso.png)

4. Configure os seguintes valores:

    ![Certificate-Setting](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Habilitar SSO:** o SSO está habilitado por padrão (clique em **Desabilitar Logon Único** para remover o requisito de SSO)

    b. Cole o valor da **URL de Logon** copiado do portal do Azure na caixa de texto **URL de Solicitação HTTP do SAML**.

    c. Abra o certificado baixado no Azure AD em um bloco de notas e copie o conteúdo, incluindo "BEGIN CERTIFICATE" e "END CERTIFICATE" e cole-o na caixa de texto **Certificado X.509**. 

    d.Clique em **Salvar Alterações**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Evernote.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Evernote**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Evernote**.

    ![O link do Evernote na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-evernote-test-user"></a>Criar um usuário de teste do Evernote

Para permitir que os usuários do Azure AD façam logon no Evernote, eles deverão ser provisionados no Evernote.  
No caso do Evernote, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Evernote como administrador.

2. Clique no **'Console de Administração'** .

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. No **'Console de Administração'** , acesse **'Adicionar usuários'** .

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Adicionar membros da equipe** na caixa de texto **Email**, digite o endereço de email da conta de usuário e clique em **Convidar.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Após o envio do convite, o titular da conta do Azure Active Directory receberá um email para aceitar o convite.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Evernote no Painel de Acesso, você deverá ser conectado automaticamente ao Evernote, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Tutorial: Integração do Azure Active Directory ao Jamf Pro | Microsoft Docs'
description: Saiba como configurar um logon único entre o Azure Active Directory e o Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d03df38dee74f4767e8ff3b21d9462fd59c4c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099579"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>Tutorial: Integração do Azure Active Directory ao Jamf Pro

Neste tutorial, você aprenderá a integrar o Jamf Pro ao Azure AD (Azure Active Directory).
A integração do Jamf Pro ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Jamf Pro.
* Você pode permitir que seus usuários entrem automaticamente no Jamf Pro (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Jamf Pro, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Jamf Pro habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Jamf Pro é compatível com SSO iniciado por **SP e IDP**

## <a name="adding-jamf-pro-from-the-gallery"></a>Como adicionar o Jamf Pro da galeria

Para configurar a integração do Jamf Pro ao AD do Azure, você precisará adicionar o Jamf Pro da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Jamf Pro por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Jamf Pro**, selecione **Jamf Pro** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Jamf Pro na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Jamf Pro, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Jamf Pro.

Para configurar e testar o logon único do Azure AD com o Jamf Pro, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Jamf Pro](#configure-jamf-pro-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Jamf Pro](#create-jamf-pro-test-user)** – para ter um equivalente de Brenda Fernandes no Jamf Pro que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Jamf Pro, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Jamf Pro**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar no modo **IDP** iniciado:

    ![Informações de logon único em Domínio e URLs do Jamf Pro](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.jamfcloud.com/saml/SSO`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<subdomain>.jamfcloud.com`

    ![Informações de logon único em Domínio e URLs do Jamf Pro](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Você obterá o valor real do Identificador de **Logon único** seção no portal de Jamf Pro, que é explicado no tutorial posteriormente. Você pode extrair o valor real **subdomínio** o valor do identificador de valor e usar as informações do **subdomínio** na URL de entrada e da URL de resposta. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-jamf-pro-single-sign-on"></a>Configurar o logon único do Jamf Pro

1. Para automatizar a configuração no Jamf Pro é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)

2. Após adicionar a extensão ao navegador, clicar em **configurar Jamf Pro** irá direcioná-lo ao aplicativo Jamf Pro. Em seguida, forneça as credenciais de administrador para entrar no Jamf Pro. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 7.

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

3. Se quiser configurar o Jamf Pro manualmente, abra uma nova janela do navegador da Web, entre no site da empresa do Jamf Pro como administrador e execute as seguintes etapas:

4. Clique no **ícone de Configurações** no canto superior direito da página.

    ![Configuração do Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Clique no **Logon Único**.

    ![Configuração do Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na página **Single Sign-On**, execute as seguintes etapas:

    ![O Jamf Pro único](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Selecione **Jamf Pro Server** para ativar o acesso ao Single Sign-On.

    b. Ao selecionar **Permitir desvio para todos os usuários**, os usuários não serão redirecionados para a página de login do Provedor de identidade para autenticação, mas poderão efetuar login no Jamf Pro diretamente. Quando um usuário tenta acessar o Jamf Pro por meio do Provedor de Identidade, ocorre a autenticação e autorização de SSO iniciada pelo IdP.

    c. Selecione a opção **NameID** para **MAPEAMENTO DE USUÁRIO: SAML**. Por padrão, essa configuração é definida como **NameID**, mas você pode definir um atributo personalizado.

    d. Selecione **Email** para **MAPEAMENTO DE USUÁRIO: JAMF PRO**. O Jamf Pro mapeia atributos SAML enviados pelo IdP das seguintes maneiras: por usuários e por grupos. Quando um usuário tenta acessar o Jamf Pro, por padrão, o Jamf Pro obtém informações sobre o usuário do Provedor de Identidade e o associa às contas de usuário do Jamf Pro. Se a conta de usuário recebida não existir no Jamf Pro, ocorrerá a correspondência do nome do grupo.

    e. Cole o valor `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` no **nome do grupo de atributo** caixa de texto.

7. Na mesma página, role a tela para baixo até **PROVEDOR DE IDENTIDADE** na seção **Logon único** e execute as seguintes etapas:

    ![Configuração do Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selecione **Outro** como opção no menu suspenso **PROVEDOR DE IDENTIDADE**.

    b. Na caixa de texto **OTHER PROVIDER**, digite **Azure AD**.

    c. Selecione **URL de metadados** como uma opção do menu suspenso **FONTE DE METADADOS DO PROVEDOR DE IDENTIDADE** e, na caixa de texto a seguir, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do portal do Azure.

    d. Copie o valor **ID da entidade** e cole-o na caixa de texto **Identifier (Entity ID)** na seção **Jamf Pro Domain e URLs** no portal do Azure.

    > [!NOTE]
    > Aqui, o valor borrado é a parte do subdomínio. Use esse valor para concluir o URL de conexão e o URL de resposta na seção **Domínio e URLs Jamf Pro** no portal do Azure.

    e. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Jamf Pro.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, selecione **Jamf Pro**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Jamf Pro**.

    ![O link do Jamf Pro na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-jamf-pro-test-user"></a>Criar um usuário de teste do Jamf Pro

Para permitir que os usuários do Azure AD façam logon no Jamf Pro, eles devem ser provisionados no Jamf Pro. No caso do Jamf Pro, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site corporativo do Jamf Pro como administrador.

2. Clique no **ícone de Configurações** no canto superior direito da página.

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Clique em **Contas de usuário e grupos do Pro Jamf**.

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/user1.png)

4. Clique em **Novo**.

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecione **Criar Conta Padrão**.

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/user3.png)

6. Na caixa de diálogo **Novo Usuário**, realize as seguintes etapas:

    ![Adicionar Funcionário](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Na caixa de texto **USERNAME**, digite o nome completo de BrendaFernandes.

    b. Selecione as opções apropriadas de acordo com a sua organização para o **NÍVEL DE ACESSO**, **CONJUNTO DE PRIVILÉGIOS** e para o **STATUS DE ACESSO**.

    c. Na caixa de texto **NOME COMPLETO**, digite o nome completo de Brenda Fernandes.

    d. Na caixa de texto **ENDEREÇO DE EMAIL**, digite o endereço de email da conta de Brenda Fernandes.

    e. Na caixa de texto **SENHA**, digite a senha do usuário.

    f. Na caixa de texto **VERIFICAR SENHA**, digite a senha do usuário.

    g. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Jamf Pro no Painel de Acesso, você deverá ser conectado automaticamente ao Jamf Pro no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

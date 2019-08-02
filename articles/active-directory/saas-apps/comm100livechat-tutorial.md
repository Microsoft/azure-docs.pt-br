---
title: 'Tutorial: Integração do Azure Active Directory com o Comm100 Live Chat | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a03b185f6df5e32012883d210d684ec08b119019
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105020"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Tutorial: Integração do Azure Active Directory com o Comm100 Live Chat

Neste tutorial, você aprenderá a integrar o Comm100 Live Chat ao Azure AD (Azure Active Directory).
A integração do Chat ao Vivo da Comm100 com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Comm100 Live Chat.
* É possível permitir que seus usuários entrem automaticamente no Comm100 Live Chat (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Comm100 Live Chat, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Comm100 Live Chat habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Comm100 Live Chat dá suporte ao SSO iniciado por **SP**

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Adicionando o Chat ao vivo do Comm100 da Galeria

Para configurar a integração do Comm100 Live Chat ao Azure AD, você precisa adicionar o Comm100 Live Chat da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Comm100 Live Chat da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Comm100 Live Chat**, selecione **Comm100 Live Chat** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

     ![Comm100 Live Chat na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Comm100 Live Chat com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Comm100 Live Chat.

Para configurar e testar o logon único do Azure AD com o Comm100 Live Chat, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Comm100 Live Chat](#configure-comm100-live-chat-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Comm100 Live Chat](#create-comm100-live-chat-test-user)** – para ter um equivalente de Brenda Fernandes no Comm100 Live Chat vinculado à representação dela no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Comm100 Live Chat, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Comm100 Live Chat**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Domínio de bate-papo ao vivo da Comm100 e informações de logon único de URLs](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > O valor da URL de logon não é real. Você atualizará o valor da URL de Logon com a URL de Logon real, que será explicada posteriormente no tutorial.

5. O aplicativo Comm100 Live Chat espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas: 

    | NOME |  Atributo de Origem|
    | ---------------| --------------- |
    |   email    | user.mail |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar Comm100 Live Chat**, copie a URL adequada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-comm100-live-chat-single-sign-on"></a>Configurar Logon único do Comm100 Live Chat

1. Em uma janela do navegador da Web diferente, faça o login no Comm100 Live Chat como um administrador de segurança.

1. No canto superior direito da página, clique em **Minha conta**.

   ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. Do lado esquerdo do menu, clique em **Segurança** e, em seguida, clique em **Logon Único do Agente**.

   ![Segurança do Chat ao Vivo da Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Sobre o **agente Single Sign-On** , execute as seguintes etapas:

   ![Segurança do Chat ao Vivo da Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Copie o primeiro link destacado e cole-o na caixa de texto **URL de login** na seção **Domínio de bate-papo ao vivo da Comm100 e URLs** no portal do Azure.

   b. Na caixa de texto **URL do SSO SAML**, cole o valor de **URL de logon**, copiado do portal do Azure.

   c. Na caixa de texto **URL de logoff remoto**, cole o valor da **URL de logoff** que você copiou do portal do Azure.

   d. Clique em **escolher um arquivo** para carregar base 64 codificados certificado que você baixou do portal do Azure, para o **certificado**.

   e. Clique em **salvar alterações**

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

Nesta seção, você deve habilitar Britta Simon usar logon único do Azure, concedendo acesso ao Comm100 a Chat ao vivo.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Comm100 Live Chat**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Comm100 Live Chat**.

    ![O link de Chat ao vivo do Comm100 na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-comm100-live-chat-test-user"></a>Criar usuário de teste do Comm100 Live Chat

Para permitir que os usuários do Azure AD efetuem login no Comm100 Live Chat, eles devem ser provisionados no Comm100 Live Chat. No Comm100 Chat ao vivo, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça o login no Comm100 Live Chat como um administrador de segurança.

2. No canto superior direito da página, clique em **Minha conta**.

    ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Do lado esquerdo do menu, clique em **Agents** e, em seguida, clique em **New Agent**.

    ![Agente do Chat ao vivo do Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Sobre o **novo agente** , execute as seguintes etapas:

    ![Chat ao vivo do Comm100 novo agente](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Na caixa de texto **Email**, insira o email do usuário como **Brendafernandes\@contoso.com**.

    b. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    c. Na **Sobrenome** texto, digite o sobrenome do usuário, como **simon**.

    d. Na caixa de texto **Display Name**, insira o nome de exibição do usuário como **Britta simon**

    e. Na caixa de texto **Senha**, digite sua senha.

    f. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Comm100 Live Chat no Painel de Acesso, você deverá ser conectado automaticamente ao Comm100 Live Chat no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


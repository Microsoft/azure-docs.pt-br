---
title: 'Tutorial: Integração do Azure Active Directory ao Elium | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b714983f1401ba60357a8642e0a56422d01be74e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103418"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Tutorial: Integração do Azure Active Directory ao Elium

Neste tutorial, você aprende a integrar o Elium ao Azure AD (Azure Active Directory).
A integração do Elium com o Microsoft Azure Active Directory oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Elium.
* Você pode permitir que seus usuários entrem automaticamente no Elium (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com o Elium, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Elium habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Elium dá suporte ao SSO iniciado por **SP** e **IDP**

* O Elium é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-elium-from-the-gallery"></a>Adição do Elium a partir da galeria

Para configurar a integração do Elium com o Microsoft Azure Active Directory, você precisa adicionar o Elium da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Elium a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Elium**, selecione **Elium** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Elium na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Elium, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Elium.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Elium, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Elium](#configure-elium-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Elium](#create-elium-test-user)** – para ter um equivalente de Brenda Fernandes no Elium que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure AD com o Elium, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Elium**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar Logon Único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do Elium](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/acs`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Elium](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE] 
    > Esses valores não são reais. Você obterá esses valores do **arquivo de metadados de SP** disponível para download em `https://<platform-domain>.elium.com/login/saml2/metadata`, o que é explicado mais adiante neste tutorial.

6. O aplicativo Elium espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas: 

    | NOME | Atributo de Origem|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > Essas são as declarações padrão. **Somente a declaração de email é necessária**. Para provisionamento de JIT, apenas a declaração de email será obrigatória também. Outras declarações personalizadas podem variar de uma plataforma de cliente para outra.

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar logon único com SAML**, na seção **Certificado de autenticação SAML**, clique em **Baixar** para baixar o **XML de metadados de federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar o Elium**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-elium-single-sign-on"></a>Configurar o Logon Único do Elium

1. Em uma janela diferente do navegador da Web, entre no site da sua empresa Elium como administrador.

2. Clique no **Perfil de usuário** no canto superior direito e selecione **Administração**.

    ![Configurar o logon único](./media/elium-tutorial/user1.png)

3. Selecione a guia **Segurança**.

    ![Configurar o logon único](./media/elium-tutorial/user2.png)

4. Role até a seção **Logon único (SSO)** e execute as seguintes etapas:

    ![Configurar o logon único](./media/elium-tutorial/user3.png)

    a. Copie o valor de **Verificar se a autenticação SAML2 funciona para sua conta** e cole-o na caixa de texto **URL de logon** na seção **Configuração SAML Básica** no portal do Azure.

    > [!NOTE]
    > Depois de configurar o SSO, você poderá sempre acessar a página de logon remoto padrão na seguinte URL: `https://<platform_domain>/login/regular/login` 

    b. Marque a caixa de seleção **Ativar federação de SAML2**.

    c. Marque a caixa de seleção **Provisionamento de JIT**.

    d. Abra o **SP Metadata** clicando no botão **Fazer o download**.

    e. Procure **entityID** no arquivo **SP Metadata**, copie o valor **entityID** e cole-o na caixa de texto **Identificador** na seção **Configuração SAML Básica** no portal do Azure. 

    ![Configurar o logon único](./media/elium-tutorial/user4.png)

    f. Procure **AssertionConsumerService** no arquivo **SP Metadata**, copie o valor **Localização** e cole-o na caixa de texto **URL de resposta** na seção **Configuração SAML Básica** no portal do Azure.

    ![Configurar o logon único](./media/elium-tutorial/user5.png)

    g. Abra o arquivo de metadados baixado do Portal do Azure no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Metadados do IdP**.

    h. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@domíniodaempresa.extensão**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Elium.

1. No portal do Azure, escolha **Aplicativos empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **Elium**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Elium**.

    ![O link do Elium na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função de usuário apropriada para o usuário na lista e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-elium-test-user"></a>Criar um usuário de teste do Elium

Nesta seção, um usuário chamado Brenda Fernandes é criado no Elium. O Elium dá suporte ao **provisionamento Just-In-Time**, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Elium, um novo será criado quando você tentar acessar o Elium.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Elium](mailto:support@elium.com).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Elium no Painel de Acesso, você deve ser conectado automaticamente ao Elium para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


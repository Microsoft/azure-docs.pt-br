---
title: 'Tutorial: Integração do Azure Active Directory ao Mozy Enterprise | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 25e223fc9e8222441e70fefb3c21f951b0ba06c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096626"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: Integração do Active Directory do Azure ao Mozy Enterprise

Neste tutorial, você aprende a integrar o Mozy Enterprise ao Azure AD (Azure Active Directory).
A integração do Mozy Enterprise ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Mozy Enterprise.
* Você pode permitir que seus usuários entrem automaticamente no Mozy Enterprise (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Mozy Enterprise, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Mozy Enterprise

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Mozy Enterprise dá suporte SSO iniciado por **SP**

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Adicionando o Mozy Enterprise por meio da galeria

Para configurar a integração do Mozy Enterprise com o Azure AD, você precisará adicionar o Mozy Enterprise à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Mozy Enterprise por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Mozy Enterprise**, selecione **Mozy Enterprise** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Mozy Enterprise na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Mozy Enterprise, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Mozy Enterprise.

Para configurar e testar o logon único do Azure AD com o Mozy Enterprise, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Mozy Enterprise](#configure-mozy-enterprise-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Mozy Enterprise](#create-mozy-enterprise-test-user)** – para ter um equivalente de Brenda Fernandes no Mozy Enterprise que está vinculado à representação desse usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Mozy Enterprise, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Mozy Enterprise**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de domínio e de URLs do Mozy Enterprise para logon único](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do Mozy Enterprise](https://support.mozy.com/) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Mozy Enterprise**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-mozy-enterprise-single-sign-on"></a>Configurar o logon único do Mozy Enterprise

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Mozy Enterprise como administrador.

2. Na seção **Configuração**, clique em **Política de Autenticação**.
   
    ![Política de autenticação](./media/mozy-enterprise-tutorial/ic777314.png "Política de autenticação")

3. Na seção **Política de Autenticação** , realize as seguintes etapas:
   
    ![Política de autenticação](./media/mozy-enterprise-tutorial/ic777315.png "Política de autenticação")
   
    a. Selecione **Serviço de Diretório** como **Provedor**.
   
    b. Selecione **Usar Push do LDAP**.
   
    c. Clique na guia **Autenticação SAML** .
   
    d. Cole a **URL de Logon**, copiada do portal do Azure, na caixa de texto **URL de Autenticação**.
   
    e. Cole o valor de **Identificador do Azure AD**, copiado do portal do Azure, na caixa de texto **Ponto de extremidade SAML**.
   
    f. Abra seu certificado codificado em Base 64 baixado no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado SAML**.
   
    g. Selecione **Habilitar SSO para que os Administradores façam logon com suas credenciais de rede**.
   
    h. Clique em **Salvar Alterações**.

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

Nesta seção, você concederá a Brenda Fernandes acesso ao Mozy Enterprise para que ela fique habilitada a usar o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **Mozy Enterprise**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Mozy Enterprise**.

    ![O link do Mozy Enterprise na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-mozy-enterprise-test-user"></a>Criar um usuário de teste do Mozy Enterprise

Para permitir que os usuários do AD do Azure façam logon no Mozy Enterprise, eles devem ser provisionados no Mozy Enterprise. No caso do Mozy Enterprise, o provisionamento é uma tarefa manual.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Mozy Enterprise ou as APIs fornecidas pelo Mozy Enterprise para provisionar as contas de usuário do AAD.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Mozy Enterprise** .

2. Clique em **Usuários** e em **Adicionar Novo Usuário**.
   
    ![Usuários](./media/mozy-enterprise-tutorial/ic777317.png "Usuários")
   
    >[!NOTE]
    >A opção **Adicionar Novo Usuário** será exibida apenas se **Mozy** estiver selecionado como o provedor em **Política de autenticação**. Se a autenticação SAML for configurada, os usuários serão adicionados automaticamente em seu primeiro logon por meio do Logon único.
    
3. Na caixa de diálogo novo usuário, execute as seguintes etapas:
   
    ![Adicionar Usuários](./media/mozy-enterprise-tutorial/ic777318.png "Adicionar Usuários")
   
    a. Na lista **Escolher um Grupo** , selecione um grupo.
   
    b. Na lista **Wue tipo de usuário** , selecione um tipo.
   
    c. Na caixa de texto **Nome do Usuário** , digite o nome do usuário do AD do Azure.
   
    d. Na caixa de texto **Email** , digite o endereço de email do usuário do Azure AD.
   
    e. Selecione **Enviar email de instruções ao usuário**.
   
    f. Clique em **Adicionar Usuário(s)** .

     >[!NOTE]
     > Após a criação do usuário, um email será enviado ao usuário do AD do Azure com um link para confirmar a conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Mozy Enterprise no Painel de Acesso, você deverá ser conectado automaticamente ao Mozy Enterprise no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


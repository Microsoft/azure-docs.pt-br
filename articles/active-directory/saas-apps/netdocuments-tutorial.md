---
title: 'Tutorial: Integração do Azure Active Directory ao NetDocuments | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 929d5d7a8e2b45aeb4ef71e4599cfcf23be83088
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096614"
---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Tutorial: Integração do Active Directory do Azure ao NetDocuments

Neste tutorial, você aprenderá a integrar o NetDocuments ao Azure AD (Azure Active Directory).
A integração do NetDocuments ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao NetDocuments.
* Você pode permitir que os usuários sejam conectados automaticamente ao NetDocuments (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao NetDocuments, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do NetDocuments habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O NetDocuments é compatível com o SSO iniciado por **SP**

## <a name="adding-netdocuments-from-the-gallery"></a>Adicionar o NetDocuments da galeria

Para configurar a integração do NetDocuments ao Azure AD, você precisará adicionar o NetDocuments da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o NetDocuments da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **NetDocuments**, selecione **NetDocuments** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![NetDocuments na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o NetDocuments, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NetDocuments.

Para configurar e testar o logon único do Azure AD com o NetDocuments, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do NetDocuments](#configure-netdocuments-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do NetDocuments](#create-netdocuments-test-user)** – para ter um equivalente de Brenda Fernandes no NetDocuments que esteja vinculado à representação desse usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o NetDocuments, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NetDocuments**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e URLs do NetDocuments](common/sp-reply.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de resposta e a URL de logon propriamente ditas. A ID do repositório é um valor que começa com **CA-** seguido por um código de oito caracteres associado a seu repositório do NetDocuments. Você pode conferir o [documento de suporte de identidade federada do NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) para obter mais informações. Como alternativa, você pode contatar a [equipe de suporte ao cliente do NetDocuments](https://support.netdocuments.com/hc/) para obter esses valores se tiver dificuldades para configurar usando as informações acima. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o NetDocuments**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-netdocuments-single-sign-on"></a>Configurar o logon único do NetDocuments

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do NetDocuments como administrador.

2. Vá para **Administrador**.

3. Clique em **Adicionar e remover usuários e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "Repositório")

4. Clique em **Configurar opções de autenticação avançadas**.
    
    ![Configurar opções de autenticação avançadas](./media/netdocuments-tutorial/ic795048.png "Configurar opções de autenticação avançadas")

5. Na caixa de diálogo **Identidade Federada**, execute as seguintes etapas:
   
    ![Identidade Federada](./media/netdocuments-tutorial/ic795049.png "Identidade Federada")
   
    a. Para **Tipo de servidor de identidade federada**, selecione **Serviços de Federação do Active Directory**.
   
    b. Clique em **Escolher arquivo**para carregar o arquivo de metadados que você baixou do Portal do Azure.
   
    c. Clique em **OK**.

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

Nesta seção, você concederá a Brenda Fernandes acesso ao NetDocuments para que ela fique habilitada a usar o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **NetDocuments**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **NetDocuments**.

    ![Link do NetDocuments na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-netdocuments-test-user"></a>Criar um usuário de teste do NetDocuments

Para permitir que os usuários do Azure AD façam logon no NetDocuments, eles devem ser provisionados no NetDocuments.  
No caso do NetDocuments, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **NetDocuments** como administrador.

2. No menu na parte superior, clique em **Administrador**.
   
    ![Admin](./media/netdocuments-tutorial/ic795051.png "Admin")

3. Clique em **Adicionar e remover usuários e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "Repositório")

4. Na caixa de texto **Endereço de Email**, digite o endereço de email de uma conta válida do Azure Active Directory que você deseja provisionar e clique em **Adicionar Usuário**.
   
    ![Endereço de Email](./media/netdocuments-tutorial/ic795053.png "Endereço de Email")
   
    >[!NOTE]
    >O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa. É possível usar qualquer outra ferramenta de criação da conta de usuário do NetDocuments ou as APIs fornecidas pelo NetDocuments para provisionar as contas de usuário do Azure Active Directory.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do NetDocuments no Painel de Acesso, você deverá ser conectado automaticamente ao NetDocuments para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


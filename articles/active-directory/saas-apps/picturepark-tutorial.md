---
title: 'Tutorial: Integração do Azure Active Directory com o Picturepark | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 32af532fcb3b3c5a294590bb7a1fa610d1068e25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094449"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integração do Azure Active Directory com o Picturepark

Neste tutorial, você aprende a integrar o Picturepark ao Azure AD (Azure Active Directory).
A integração do Picturepark ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Picturepark.
* Você pode permitir que os usuários sejam conectados automaticamente ao Picturepark (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Picturepark, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Picturepark

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Picturepark dá suporte ao SSO iniciado por **SP**

## <a name="adding-picturepark-from-the-gallery"></a>Adicionando o Picturepark por meio da galeria

Para configurar a integração do Picturepark ao Azure AD, é necessário adicionar o Picturepark à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Picturepark por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Picturepark**, selecione **Picturepark** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Picturepark na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Picturepark, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Picturepark.

Para configurar e testar o logon único do Azure AD com o Picturepark, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Picturepark](#configure-picturepark-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Picturepark](#create-picturepark-test-user)** – para ter um equivalente de Brenda Fernandes no Picturepark que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Picturepark, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Picturepark**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Picturepark](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.picturepark.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do Picturepark](https://picturepark.com/about/contact/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

6. Na seção **Certificado de Autenticação SAML**, copie a **Impressão Digital** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

7. Na seção **Configurar o Picturepark**, copie as URLs apropriadas de acordo com suas necessidades. Em **URL de Logon**, use o valor com o seguinte padrão: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é a ID do locatário da assinatura do Microsoft Directory Azure.

    ![Copiar URLs de configuração](./media/picturepark-tutorial/configurls.png)

    a. Identificador do Azure AD

    b. URL de logoff

### <a name="configure-picturepark-single-sign-on"></a>Configurar o logon único do Picturepark

1. Em uma janela de navegador da Web diferente, entre no site de sua empresa do Picturepark como administrador.

2. Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas** e em **Console de Gerenciamento**.
   
    ![Console de Gerenciamento](./media/picturepark-tutorial/ic795062.png "Console de Gerenciamento")

3. Clique em **Autenticação** e em **Provedores de identidade**.
   
    ![Autenticação](./media/picturepark-tutorial/ic795063.png "Autenticação")

4. Na seção **Configuração do provedor de identidade** , realize as seguintes etapas:
   
    ![Configuração do Provedor de Identidade](./media/picturepark-tutorial/ic795064.png "Configuração do Provedor de Identidade")
   
    a. Clique em **Adicionar**.
  
    b. Digite um nome para sua configuração.
   
    c. Selecione **Definir como padrão**.
   
    d. Na caixa de texto **URI do emissor**, cole o valor da **URL de Logon** copiado do portal do Azure.
   
    e. Na caixa de texto **Impressão Digital do Emissor Confiável**, cole o valor da **Impressão Digital** copiado da seção **Certificado de Autenticação SAML**. 

5. Clique em **JoinDefaultUsersGroup**.

6. Para definir o atributo **Emailaddress** na caixa de texto **Declaração**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` e clique em **Salvar**.

      ![Configuração](./media/picturepark-tutorial/ic795065.png "Configuração")

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Picturepark.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Picturepark**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Picturepark**.

    ![O link Picturepark na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-picturepark-test-user"></a>Criar um usuário de teste do Picturepark

Para permitir que os usuários do Azure AD entrem no Picturepark, eles devem ser provisionados no Picturepark. No caso do Picturepark, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no seu locatário do **Picturepark** .

1. Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas** e em **Usuários**.
   
    ![Usuários](./media/picturepark-tutorial/ic795067.png "Usuários")

1. Na guia **Visão geral de usuários**, clique em **Novo**.
   
    ![Gerenciamento de usuário](./media/picturepark-tutorial/ic795068.png "Gerenciamento de usuário")

1. Na caixa de diálogo **Criar Usuário**, realize as seguintes etapas de um Usuário válido do Azure Active Directory que você deseja provisionar:
   
    ![Criar usuário](./media/picturepark-tutorial/ic795069.png "Criar usuário")
   
    a. Na caixa de texto **Endereço de Email**, digite o **endereço de email** do usuário `BrittaSimon@contoso.com`.  
   
    b. Nas caixas de texto **Senha** e **Confirmar Senha**, digite a **senha** de BrendaFernandes. 
   
    c. Na caixa de texto **Nome**, digite o **Nome** do usuário **Brenda**. 
   
    d. Na caixa de texto **Sobrenome**, digite o **Sobrenome** do usuário **Fernandes**.
   
    e. Na caixa de texto **Empresa**, digite o **Nome da empresa** do usuário. 
   
    f. Na caixa de texto **País**, selecione o **País/Região** do usuário.
  
    g. Na caixa de texto **CEP**, digite o **CEP** da cidade.
   
    h. Na caixa de texto **Cidade**, digite o **Nome da cidade** do usuário.

    i. Selecione um **Idioma**.
   
    j. Clique em **Criar**.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do Picturepark ou as APIs fornecidas pelo Picturepark para provisionar contas de usuário do Azure AD.
>

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça do Picturepark no Painel de Acesso, você deverá ser conectado automaticamente ao Picturepark, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


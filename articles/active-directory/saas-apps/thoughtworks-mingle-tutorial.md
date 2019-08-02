---
title: 'Tutorial: Integração do Azure Active Directory ao Thoughtworks Mingle | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 4ea2a88c92dcd52e99bb98563cf76d2c5501ef6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088758"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: Integração do Azure Active Directory ao Thoughtworks Mingle

Neste tutorial, você aprenderá a integrar o Thoughtworks Mingle ao Azure AD (Azure Active Directory).
A integração do Thoughtworks Mingle ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Thoughtworks Mingle.
* Você pode permitir que os usuários entrem automaticamente no Thoughtworks Mingle (Logon Único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Thoughtworks Mingle, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Thoughtworks Mingle

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Thoughtworks Mingle é compatível com SSO iniciado por **SP**

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Adicionar o Thoughtworks Mingle da galeria

Para configurar a integração do Thoughtworks Mingle ao Azure AD, você precisa adicionar o Thoughtworks Mingle da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Thoughtworks Mingle da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Thoughtworks Mingle**, selecione **Thoughtworks Mingle** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ThoughtWorks Mingle na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Thoughtworks Mingle, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Thoughtworks Mingle.

Para configurar e testar o logon único do Azure AD com o Thoughtworks Mingle, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Thoughtworks Mingle](#configure-thoughtworks-mingle-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Thoughtworks Mingle](#create-thoughtworks-mingle-test-user)** – para ter um equivalente de Brenda Fernandes no Thoughtworks Mingle vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Thoughtworks Mingle, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Thoughtworks Mingle**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Thoughtworks Mingle](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do Thoughtworks Mingle](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Thoughtworks Mingle**, copie as URLs corretas, de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Configurar o logon único do Thoughtworks Mingle

1. Entre no site da empresa do **Thoughtworks Mingle** como um administrador.

2. Clique na guia **Administrador** e em **Config. de SSO**.
   
    ![Guia Administrador](./media/thoughtworks-mingle-tutorial/ic785157.png "Configuração de SSO")

3. Na seção **Config. de SSO** , realize as seguintes etapas:
   
    ![Configuração de SSO](./media/thoughtworks-mingle-tutorial/ic785158.png "Configuração de SSO")
    
    a. Para carregar o arquivo de metadados, clique em **Escolher arquivo**. 

    b. Clique em **Salvar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Thoughtworks Mingle.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **Thoughtworks Mingle**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Thoughtworks Mingle**.

    ![O link do Thoughtworks Mingle na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-thoughtworks-mingle-test-user"></a>Criar um usuário de teste do Thoughtworks Mingle

Para usuários do Azure AD conseguirem entrar, eles devem ser provisionados para o aplicativo Thoughtworks Mingle usando seus nomes de usuário do Azure Active Directory. No caso do Thoughtworks Mingle, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Entre no site da empresa do Thoughtworks Mingle como um administrador.

2. Clique em **Perfil**.
   
    ![Seu primeiro projeto](./media/thoughtworks-mingle-tutorial/ic785160.png "Seu primeiro projeto")

3. Clique na guia **Administrador** e em **Usuários**.
   
    ![Usuários](./media/thoughtworks-mingle-tutorial/ic785161.png "Usuários")

4. Clique em **Novo Usuário**.
   
    ![Novo Usuário](./media/thoughtworks-mingle-tutorial/ic785162.png "Novo Usuário")

5. Na página do diálogo **Novo Usuário** , realize as seguintes etapas:
   
    ![Caixa de diálogo Novo Usuário](./media/thoughtworks-mingle-tutorial/ic785163.png "Novo Usuário")  
 
    a. Digite o **Nome de entrada**, **Nome de exibição**, **Escolher senha** e **Confirmar senha** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas. 

    b. Para **Tipo de usuário**, selecione **Usuário completo**.

    c. Clique em **Criar este Perfil**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Thoughtworks Mingle ou as APIs fornecidas pelo Thoughtworks Mingle para provisionar as contas de usuário do AAD.
> 

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Thoughtworks Mingle no Painel de Acesso, você deverá ser conectado automaticamente ao Thoughtworks Mingle no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


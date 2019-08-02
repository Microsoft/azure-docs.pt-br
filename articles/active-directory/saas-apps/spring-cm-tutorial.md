---
title: 'Tutorial: Integração do Azure Active Directory com SpringCM | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e1d1973dd51068e6f3e0746ee988a51f375899f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588011"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutorial: Integração do Azure Active Directory com SpringCM

Neste tutorial, você aprenderá a integrar o SpringCM ao Azure AD (Azure Active Directory).
A integração do SpringCM ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao SpringCM.
* Você pode permitir que os usuários sejam conectados automaticamente ao SpringCM (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SpringCM, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do SpringCM

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SpringCM dá suporte ao SSO iniciado por **SP**

## <a name="adding-springcm-from-the-gallery"></a>Adicionar o SpringCM da galeria

Para configurar a integração do SpringCM ao Azure AD, você precisará adicionar o SpringCM da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SpringCM da galeria, execute as seguintes etapas:**

1. No **[portal do Microsoft Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SpringCM**, selecione **SpringCM** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SpringCM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SpringCM, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SpringCM.

Para configurar e testar o logon único do Azure AD com o SpringCM, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do SpringCM](#configure-springcm-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SpringCM](#create-springcm-test-user)** – para ter um equivalente de Brenda Fernandes no SpringCM que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SpringCM, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SpringCM**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SpringCM](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do SpringCM](https://knowledge.springcm.com/support) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Bruto)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

6. Na seção **Configurar o SpringCM**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-springcm-single-sign-on"></a>Configurar o logon único do SpringCM

1. Em outra janela do navegador da Web, entre em seu site de empresa do **SpringCM** como administrador.

1. No menu na parte superior, clique em **IR PARA**, clique em **Preferências** e, na seção **Preferências da Conta**, clique em **SSO do SAML**.
   
    ![SSO do SAML](./media/spring-cm-tutorial/ic797051.png "SSO do SAML")

1. Na seção Configuração do Provedor de Identidade, execute as seguintes etapas:
   
    ![Configuração do Provedor de Identidade](./media/spring-cm-tutorial/ic797052.png "Configuração do Provedor de Identidade")
    
    a. Para carregar seu certificado baixado do Azure Active Directory, clique em **Selecionar Certificado do Emissor** ou **Alterar Certificado do Emissor**.
    
    b. Na caixa de texto **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.
    
    c. Na caixa de texto **Ponto de Extremidade Iniciado pelo SP (Provedor de Serviços)** , cole o valor da **URL de Logon** copiado do portal do Azure.
            
    d. Selecione **SAML Habilitado** como **Habilitar**.

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
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao SpringCM.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SpringCM**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SpringCM**.

    ![O link do SpringCM na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-springcm-test-user"></a>Criar um usuário de teste do SpringCM

Para permitir que os usuários do Azure Active Directory entrem no SpringCM, eles precisam ser provisionados no SpringCM. No caso do SpringCM, o provisionamento será uma tarefa manual.

> [!NOTE]
> Para obter mais informações, veja [Criar e editar um usuário do SpringCM](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Para provisionar uma conta de usuário no SpringCM, execute as seguintes etapas:**

1. Entre em seu site de empresa do **SpringCM** como administrador.

1. Clique em **IR PARA** e depois em **CATÁLOGO DE ENDEREÇOS**.
   
    ![Criar usuário](./media/spring-cm-tutorial/ic797054.png "Criar usuário")

1. Clique em **Criar Usuário**.

1. Selecione uma **Função de Usuário**.

1. Selecione **Enviar Email de Ativação**.

1. Digite o nome, o sobrenome e o endereço de email de uma conta de usuário válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

1. Adicione o usuário a um **Grupo de segurança**.

1. Clique em **Save** (Salvar).

   > [!NOTE]
   > É possível usar qualquer outra ferramenta de criação da conta de usuário do SpringCM ou as APIs fornecidas pelo SpringCM para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SpringCM no Painel de Acesso, você deverá ser conectado automaticamente ao SpringCM, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


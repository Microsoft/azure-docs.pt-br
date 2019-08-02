---
title: 'Tutorial: Integração do Azure Active Directory ao Rally Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Rally Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: de84d03c3e0e433dbe7bc24c47b1766b32ad7bc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093169"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integração do Azure Active Directory ao Rally Software

Neste tutorial, você aprenderá a integrar o Rally Software ao Azure AD (Azure Active Directory).
A integração do Rally Software ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Rally Software.
* Você pode permitir que os usuários sejam conectados automaticamente ao Rally Software (logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Rally Software, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Rally Software habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Rally Software é compatível com SSO iniciado por **SP**

## <a name="adding-rally-software-from-the-gallery"></a>Adicionar o Rally Software por meio da galeria

Para configurar a integração do Rally Software com o Azure AD, você precisará adicionar o Rally Software à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Rally Software por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Rally Software**, selecione **Rally Software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Rally Software na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Rally Software com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Rally Software.

Para configurar e testar o logon único do Azure AD com o Rally Software, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Rally Software](#configure-rally-software-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do Rally Software](#create-rally-software-test-user)** – para ter um equivalente de Brenda Fernandes no Rally Software que esteja vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Rally Software, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **Rally Software**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Rally Software](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.rally.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do Rally Software](https://help.rallydev.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar Rally Software**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-rally-software-single-sign-on"></a>Configurar o logon único do Rally Software

1. Entre com seu locatário do **Rally Software**.

2. Na barra de ferramentas na parte superior, clique em **Instalação** e selecione **Assinatura**.
   
    ![Assinatura](./media/rally-software-tutorial/ic769531.png "Assinatura")

3. Clique no botão **Ação**. Selecione **Editar Assinatura** no canto superior direito da barra de ferramentas.

4. Na página de diálogo **Assinatura**, realize as seguintes etapas e clique em **Salvar e Fechar**:
   
    ![Autenticação](./media/rally-software-tutorial/ic769542.png "Autenticação")
   
    a. Selecione **Autenticação do Rally ou SSO** na lista suspensa Autenticação.

    b. Na caixa de texto **URL do provedor de identidade**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure. 

    c. Na caixa de texto **Logoff do SSO**, cole o valor da **URL de Logoff** copiado do portal do Azure.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Rally Software.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Rally Software**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Rally Software**.

    ![O link do Rally Software na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-rally-software-test-user"></a>Criar um usuário de teste do Rally Software

Para usuários do Azure AD conseguirem entrar, eles devem ser provisionados para o aplicativo Rally Software usando os respectivos nomes de usuário do Azure Active Directory.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Entre com seu locatário do Rally Software.

2. Vá para **Instalação \> USUÁRIOS** e clique em **+ Adicionar Novo**.
   
    ![Usuários](./media/rally-software-tutorial/ic781039.png "Usuários")

3. Digite o nome na caixa de texto Novo Usuário e clique em **Adicionar com Detalhes**.

4. Na seção **Criar Usuário** , realize as seguintes etapas:
   
    ![Criar usuário](./media/rally-software-tutorial/ic781040.png "Criar usuário")

    a. Na caixa de texto **Nome de Usuário**, digite o nome de usuário como **Brenda Fernandes**.
   
    b. Na caixa de texto **Endereço de Email**, insira o email do usuário como brittasimon@contoso.com.

    c. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda**.

    d. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

    e. Clique em **Salvar e Fechar**.

   >[!NOTE]
   >É possível usar qualquer outra ferramenta de criação da conta de usuário do Rally Software ou as APIs fornecidas pelo Rally Software para provisionar as contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Rally Software no Painel de Acesso, você deverá ser conectado automaticamente ao Rally Software para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Tutorial: Integração do Azure Active Directory com o Igloo Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Igloo Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: df1d70f895e2e0a81344cf2a4e8e2d9963c951fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100592"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Integração do Azure Active Directory com o Igloo Software

Neste tutorial, você aprenderá a integrar o Igloo Software ao Azure AD (Azure Active Directory).
A integração do Igloo Software ao Azure AD oferece os seguintes benefícios:

* Você pode controlar, no Azure AD, quem tem acesso ao Igloo Software.
* Você pode permitir que os usuários sejam conectados automaticamente ao Igloo Software (logon único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Igloo Software, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Igloo Software habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Igloo Software dá suporte a SSO iniciado por **SP**
* O Igloo Software é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-igloo-software-from-the-gallery"></a>Adicionando Igloo Software da galeria

Para configurar a integração do Igloo Software ao Azure AD, você precisa adicionar o Igloo Software da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Igloo Software da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Igloo Software**, selecione **Igloo Software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Igloo Software na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Igloo Software com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Igloo Software.

Para configurar e testar o logon único do Azure AD com o Igloo Software, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Igloo Software](#configure-igloo-software-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Igloo Software](#create-igloo-software-test-user)** – para ter um equivalente de Brenda Fernandes no Igloo Software que esteja vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Igloo Software, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **Igloo Software**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e de URLs do Igloo Software](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<company name>.igloocommmunities.com`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://<company name>.igloocommmunities.com/saml.digest`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao Cliente do Igloo Software](https://www.igloosoftware.com/services/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Igloo Software**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-igloo-software-single-sign-on"></a>Configurar o logon único do Igloo Software

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Igloo Software como administrador.

2. Vá para o **Painel de Controle**.

     ![Painel de controle](./media/igloo-software-tutorial/ic799949.png "Painel de controle")

3. Na guia **Associação**, clique em **Configurações de Entrada**.

    ![Configurações de entrada](./media/igloo-software-tutorial/ic783968.png "Configurações de entrada")

4. Na seção Configuração do SAML, clique em **Configurar Autenticação SAML**.

    ![Configuração SAML](./media/igloo-software-tutorial/ic783969.png "configuração SAML")

5. Na seção **Configuração Geral** , realize as seguintes etapas:

    ![Configuração geral](./media/igloo-software-tutorial/ic783970.png "Configuração geral")

    a. Na caixa de texto **Nome da Conexão** , digite um nome personalizado para a sua configuração.

    b. Na caixa de texto **URL de Logon de IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logoff de IdP**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Selecione **Tipo HTTP de Solicitação e Resposta de Logoff** como **POST**.

    e. Abra seu certificado codificado em **base-64** no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado Público**.

6. Em **Configuração de Resposta e Autenticação**, realize as seguintes etapas:

    ![Configuração de autenticação e resposta](./media/igloo-software-tutorial/IC783971.png "Configuração de autenticação e resposta")
  
    a. Para **Provedor de Identidade**, selecione **Microsoft ADFS**.

    b. Para **Tipo de Identificador**, selecione **Endereço de Email**. 

    c. Na caixa de texto **Atributo de Email**, digite **emailaddress**.

    d. Na caixa de texto **Atributo de Nome**, digite **givenname**.

    e. Na caixa de texto **Atributo de Sobrenome**, digite **surname**.

7. Execute as seguintes etapas para concluir a configuração:

    ![Criação de usuário na entrada](./media/igloo-software-tutorial/IC783972.png "Criação de usuário na entrada") 

    a. Para **Criação de usuário na Entrada**, selecione **Criar um novo usuário em seu site ao entrar**.

    b. Para **Configurações de Entrada**, selecione **Usar botão do SAML na tela “Entrar”** .

    c. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao Igloo Software.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Igloo Software**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Igloo Software**.

    ![O link do Igloo Software na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-igloo-software-test-user"></a>Criar um usuário de teste do Igloo Software

Não há item de ação para a configuração do provisionamento de usuário para o Igloo Software.  

Quando um usuário atribuído tenta fazer logon no Igloo Software usando o painel de acesso, o Igloo Software verifica se o usuário existe.  Se ainda não houver conta de usuário, ela será criada automaticamente pelo Igloo Software.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Igloo Software no Painel de Acesso, você deverá ser conectado automaticamente ao Igloo Software para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
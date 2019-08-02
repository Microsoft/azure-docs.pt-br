---
title: 'Tutorial: Integração do Azure Active Directory com SilkRoad Life Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o SilkRoad Life Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 63165da69815c77afb8692e1e68c1710beb8df8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090835"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Integração do Azure Active Directory com SilkRoad Life Suite

Neste tutorial, você aprenderá a integrar o SilkRoad Life Suite ao Microsoft Azure AD (Azure Active Directory).
A integração do SilkRoad Life Suite ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure AD quem tem acesso ao SilkRoad Life Suite.
* Você pode permitir que os usuários sejam conectados automaticamente ao SilkRoad Life Suite (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o SilkRoad Life Suite, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura habilitada para logon único do SilkRoad Life Suite

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SilkRoad Life Suite dá suporte ao SSO iniciado por **SP**

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Adicionando o SilkRoad Life Suite por meio da galeria

Para configurar a integração do SilkRoad Life Suite com o AD do Azure, você precisa adicionar o SilkRoad Life Suite por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SilkRoad Life Suite por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SilkRoad Life Suite**, selecione **SilkRoad Life Suite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SilkRoad Life Suit na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o SilkRoad Life Suite, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SilkRoad Life Suite.

Para configurar e testar o logon único do AD do Azure com o SilkRoad Life Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do SilkRoad Life Suite](#configure-silkroad-life-suite-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SilkRoad Life Suite](#create-silkroad-life-suite-test-user)** – para ter um equivalente de Brenda Fernandes no SilkRoad Life Suite que esteja vinculado à representação dela no Microsoft Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SilkRoad Life Suite, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SilkRoad Life Suite**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    > [!NOTE]
    > Você obterá o **arquivo de metadados do provedor de serviços** explicado posteriormente neste tutorial.

    a. Clique em **Carregar arquivo de metadados**.

    ![image](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![image](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na seção Configuração Básica do SAML:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Se os valores de **Identificador** e **URL de resposta** não forem preenchidos automaticamente, preencha-os manualmente de acordo com seus requisitos.

    d. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Na seção **Configuração básica do SAML**, se você não tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    ![Informações de logon único de URLs e Domínio do SilkRoad Life Suite](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao Cliente do SilkRoad Life Suite](https://www.silkroad.com/locations/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o SilkRoad Life Suite**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Configurar o logon único do SilkRoad Life Suite

1. Entre no site da empresa SilkRoad como administrador.

    > [!NOTE]
    > Para obter acesso ao aplicativo de Autenticação do SilkRoad Life Suite para configurar a federação com o AD do Microsoft Azure, entre em contato com o Suporte da SilkRoad ou com seu representante de Serviços da SilkRoad.

1. Vá para **Provedor de Serviços** e clique em **Detalhes de Federação**.

    ![Logon Único do AD do Azure](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Clique em **Baixar Metadados de Federação**e salve o arquivo de metadados no computador. Use metadados de federação baixados como um **arquivo de metadados do provedor de serviços** na seção **Configuração básica do SAML** no portal do Azure.

    ![Logon Único do AD do Azure](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. No aplicativo **SilkRoad**, clique em **Fontes de Autenticação**.

    ![Logon Único do AD do Azure](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Clique em **Adicionar Fonte de Autenticação**.

    ![Logon Único do AD do Azure](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Na seção **Adicionar Fonte de Autenticação** , execute as seguintes etapas:

    ![Logon Único do AD do Azure](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Em **Opção 2 – Arquivo de Metadados**, clique em **Procurar** para carregar o arquivo de metadados baixado a partir do portal do Azure.
  
    b. Clique em **Criar Provedor de Identidade usando Dados de Arquivo**.

1. Na seção **Fontes de Autenticação**, clique em **Editar**.

    ![Logon Único do AD do Azure](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. No diálogo **Editar Fonte de Autenticação** , execute as seguintes etapas:

    ![Logon Único do AD do Azure](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Para **Habilitado**, selecione **Sim**.

    b. Na caixa de texto **ID da Entidade**, cole o valor do **Identificador do Azure AD** que você copiou do portal do Azure.

    c. Na caixa de texto **Descrição do IdP**, digite uma descrição para a configuração (por exemplo: *SSO do Azure AD*).

    d. Na caixa de texto **Arquivo de metadados**, carregue o arquivo de **metadados** baixado no portal do Azure.
  
    e. Na caixa de texto **Nome do IdP**, digite um nome específico para a configuração (por exemplo: *SP do Azure*).
  
    f. Na caixa de texto **URL do serviço de logout**, cole o valor de **URL de logout** copiado do portal do Azure.

    g. Na caixa de texto **URL de Serviço de Logon**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    h. Clique em **Save** (Salvar).

1. Desabilite todas as outras fontes de autenticação.

    ![Logon Único do AD do Azure](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SilkRoad Life Suite.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SilkRoad Life Suite**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SilkRoad Life Suite**.

    ![Link do SilkRoad Life Suite na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-silkroad-life-suite-test-user"></a>Criar um usuário de teste do SilkRoad Life Suite

Nesta seção, você cria um usuário chamado Brenda Fernandes no SilkRoad Life Suite. Trabalhe com a [equipe de suporte ao cliente do SilkRoad Life Suite](https://www.silkroad.com/locations/) para adicionar os usuários na plataforma do SilkRoad Life Suite. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SilkRoad Life Suite no Painel de Acesso, você deverá ser conectado automaticamente ao SilkRoad Life Suite, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

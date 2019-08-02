---
title: 'Tutorial: Integração do Azure Active Directory com o PolicyStat | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 92c2da7c248a9737ab0ead35e0d4a9cae87f4588
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094319"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integração do Azure Active Directory com o PolicyStat

Neste tutorial, você aprende a integrar o PolicyStat ao Azure AD (Azure Active Directory).
A integração do PolicyStat ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao PolicyStat.
* Você pode permitir que os usuários sejam conectados automaticamente ao PolicyStat (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao PolicyStat, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do PolicyStat

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O PolicyStat é compatível com SSO iniciado por **SP**

* O PolicyStat é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-policystat-from-the-gallery"></a>Adicionando o PolicyStat por meio da galeria

Para configurar a integração do PolicyStat ao Azure AD, é necessário adicionar o PolicyStat à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o PolicyStat por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **PolicyStat**, selecione **PolicyStat** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![PolicyStat na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o PolicyStat com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PolicyStat.

Para configurar e testar o logon único do Azure AD com o PolicyStat, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do PolicyStat](#configure-policystat-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do PolicyStat](#create-policystat-test-user)** – para ter um equivalente de Brenda Fernandes no PolicyStat que esteja vinculado à representação do usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o PolicyStat, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **PolicyStat**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do PolicyStat](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.policystat.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do PolicyStat](http://www.policystat.com/support/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

5. Seu aplicativo PolicyStat espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário** .

    ![image](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo PolicyStat espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Atributo de Origem |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.
    
    ![image](common/new-save-attribute.png)

    ![image](./media/policystat-tutorial/attribute01.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Selecione Fonte como **Transformação**.

    e. Na lista **Transformação**, digite o valor do atributo mostrado para aquela linha.
    
    f. Na lista **Parâmetro 1**, digite o valor do atributo mostrado para essa linha.

    g. Clique em **Save** (Salvar).

7. Na seção **Configurar o PolicyStat**, copie as URLs apropriadas, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-policystat-single-sign-on"></a>Configurar o logon único do PolicyStat

1. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do PolicyStat como administrador.

2. Clique na guia **Admin** e, em seguida, clique em **Configuração de Logon Único** no painel de navegação à esquerda.
   
    ![Menu Administrador](./media/policystat-tutorial/ic808633.png "Menu Administrador")

3. Na seção **Configuração**, selecione **Habilitar Integração de Logon Único**.
   
    ![Configuração de Logon Único](./media/policystat-tutorial/ic808634.png "Configuração de Logon Único")

4. Clique em **Configurar Atributos** e, em seguida, na seção **Configurar Atributos**, realize as seguintes etapas:
   
    ![Configuração de Logon Único](./media/policystat-tutorial/ic808635.png "Configuração de Logon Único")
   
    a. Na caixa de texto **Atributo do Nome de Usuário**, digite **uid**.

    b. Na caixa de texto **Atributo de Nome**, digite o **firstname** do usuário **Brenda**.

    c. Na caixa de texto **Atributo de Sobrenome**, digite o **lastname** do usuário **Fernandes**.

    d. Na caixa de texto **Atributo de Email**, digite o **emailaddress** do usuário `BrittaSimon@contoso.com`.

    e. Clique em **Salvar Alterações**.

5. Clique em **Seus Metadados do IDP** e, na seção **Seus Metadados do IDP**, realize as seguintes etapas:
   
    ![Configuração de Logon Único](./media/policystat-tutorial/ic808636.png "Configuração de Logon Único")
   
    a. Abra o arquivo de metadados baixado, copie o conteúdo e, depois, cole-o na caixa de texto **Metadados do Provedor de Identidade**.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao PolicyStat.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **PolicyStat**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **PolicyStat**.

    ![O link do PolicyStat na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-policystat-test-user"></a>Criar um usuário de teste do PolicyStat

Nesta seção, um usuário chamado Brenda Fernandes será criado no PolicyStat. O PolicyStat é compatível com provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no PolicyStat, um novo será criado após a autenticação.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do PolicyStat ou as APIs fornecidas pelo PolicyStat para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do PolicyStat no painel de acesso, você será conectado automaticamente ao PolicyStat no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


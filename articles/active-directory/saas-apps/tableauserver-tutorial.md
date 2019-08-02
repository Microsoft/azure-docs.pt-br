---
title: 'Tutorial: Integração do Azure Active Directory ao Tableau Server | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: ed8e63a49702e8ba951990f8d7ff19b8f058fa74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089400"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integração do Azure Active Directory ao Tableau Server

Neste tutorial, você aprenderá a integrar o Tableau Server ao Azure AD (Azure Active Directory).
A integração do Tableau Server ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure Active Directory quem tem acesso ao Tableau Server.
* É possível permitir que seus usuários entrem automaticamente no Tableau Server (Logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Tableau Server, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Tableau Server

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Tableau Server é compatível com SSO iniciado por **SP**

## <a name="adding-tableau-server-from-the-gallery"></a>Adição do Tableau Server a partir da galeria

Para configurar a integração do Tableau Server ao Azure AD, você precisa adicionar o Tableau Server da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Tableau Server por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Tableau Server**, selecione **Tableau Server** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Tableau Server na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Tableau Server com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tableau Server.

Para configurar e testar o logon único do Azure AD com o Tableau Server, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar Logon Único do Tableau Server](#configure-tableau-server-single-sign-on)** - para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Tableau Server](#create-tableau-server-test-user)** – para ter um equivalente de Brenda Fernandes no Tableau Server que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Tableau Server, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **Tableau Server**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Tableau Server](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://azure.<domain name>.link`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://azure.<domain name>.link`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Os valores anteriores não são valores reais. Atualize os valores com o URL e o identificador reais na página de configuração do Tableau Server, explicada posteriormente no tutorial.

5. O aplicativo do Tableau Server espera uma declaração personalizada **nome de usuário** que precisa ser definida como abaixo. Isso está sendo usado como identificador de usuários, em vez de uma reivindicação exclusiva do identificador de usuários. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Clique no botão **Editar** para abrir a caixa de diálogo **Atributos do usuário e reivindicações**.

    ![image](common/edit-attribute.png)

6. Na seção **Reivindicações do usuário** da caixa de diálogo **Atributos e reivindicações do usuário**, configure o atributo do token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | NOME | Atributo de Origem | Namespace |
    | ---------------| --------------- | ----------- |
    | Nome de Usuário | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Digite o valor do **Namespace**.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

7. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

8. Na seção **Configurar o Tableau Server**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-tableau-server-single-sign-on"></a>Configurar Logon Único do Tableau Server

1. Para configurar o SSO para seu aplicativo, você precisa entrar no locatário Tableau Server como administrador.

2. Na guia **CONFIGURAÇÃO**, selecione **Identidade e acesso do usuário** e, em seguida, selecione a guia Método de **Autenticação**.

    ![Configurar o logon único](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na página **CONFIGURAÇÃO**, realize as seguintes etapas:

    ![Configurar o logon único](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Para **Método de Autenticação**, selecione SAML.

    b. Selecione a caixa de seleção **Habilitar a autenticação SAML para o servidor**.

    c. URL de retorno do Tableau Server — a URL que os usuários do Tableau Server acessarão, por exemplo <http://tableau_server>. Usar `http://localhost` não é recomendado. Não há suporte para usar uma URL com uma barra à direita (por exemplo, `http://tableau_server/`). Copie **URL de retorno do Tableau Server** e cole-o na caixa de texto **URL de Logon** na seção **Configuração Básica de SAML** no portal do Azure

    d. ID de entidade SAML - a ID de entidade identifica com exclusividade sua instalação do Tableau Server para o IdP. Você pode digitar a URL do Tableau Server novamente aqui, se desejar, mas ela não precisa ser sua URL do Tableau Server. Copie **ID da entidade SAML** e cole-a na caixa de texto **Identificador** na seção **Configuração Básicas de SAML** no portal do Azure

    e. Clique em **Baixar Arquivo de Metadados XML** e abra-o no aplicativo de editor de texto. Localize a URL do Serviço de Declaração do Consumidor com Http Post e Índice 0 e copie a URL. Agora, cole-o na caixa de texto **URL de Resposta** na seção **Configuração Básicas de SAML** no portal do Azure

    f. Localize o arquivo de Metadados de Federação baixado do portal do Azure e carregue-o no **arquivo de metadados do IDP do SAML**.

    g. Insira os nomes dos atributos que o IdP usa para manter os nomes de usuário, nomes de exibição e endereços de email.

    h. Clique em **Salvar**

    > [!NOTE]
    > O cliente precisa carregar um certificado na configuração de SSO do SAML do Tableau Server e ele será ignorado no fluxo de SSO. Se precisar de ajuda para configurar o SAML no Tableau Server, consulte o artigo [Configurar SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Tableau Server.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Tableau Server**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Tableau Server**.

    ![O link do Tableau Server na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-tableau-server-test-user"></a>Criar um usuário de teste do Tableau Server

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no Tableau Server. Será necessário provisionar todos os usuários no Tableau Server.

O nome de usuário deve corresponder ao valor que você configurou no atributo personalizado **username** do Azure AD. Com o mapeamento correto, a integração deve funcionar, conforme a Configuração do Logon Único do Azure AD.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com o administrador do Tableau Server em sua organização.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Tableau Server no Painel de Acesso, você deverá entrar automaticamente no Tableau Server para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


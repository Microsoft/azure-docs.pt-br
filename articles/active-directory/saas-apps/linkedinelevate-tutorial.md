---
title: 'Tutorial: Integração do Azure Active Directory com o LinkedIn Elevate | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Elevate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b16d928e2e4fc4b5376d30b81eb39e5a46e54b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098038"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Tutorial: Integração do Azure Active Directory com o LinkedIn Elevate

Neste tutorial, você aprenderá a integrar o LinkedIn Elevate ao Azure AD (Azure Active Directory).
A integração do LinkedIn Elevate com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao LinkedIn Elevate.
* É possível permitir que seus usuários entrem automaticamente no LinkedIn Elevate (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o LinkedIn Elevate, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura do LinkedIn Elevate habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O LinkedIn Elevate é compatível com SSO iniciado por **SP e IDP**

* O LinkedIn Elevate é compatível com o provisionamento de usuário **Just-In-Time**

* O LinkedIn Elevate é compatível com o [provisionamento de usuário **automatizado**](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Adicionando o LinkedIn da galeria

Para configurar a integração do LinkedIn Elevate ao Azure AD, é necessário adicionar o LinkedIn Elevate por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o LinkedIn Elevate da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **LinkedIn Elevate**, selecione **LinkedIn Elevate** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![LinkedIn Elevate na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o LinkedIn Elevate com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LinkedIn Elevate.

Para configurar e testar o logon único do Azure AD com o LinkedIn Elevate, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único do LinkedIn Elevate](#configure-linkedin-elevate-single-sign-on)** – para configurar as definições de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do LinkedIn Elevate](#create-linkedin-elevate-test-user)** – para ter um equivalente de Brenda Fernandes no LinkedIn Elevate que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o LinkedIn Elevate, siga as etapas abaixo:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **LinkedIn Elevate**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do LinkedIn Elevate](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, insira o valor **ID da Entidade**, você copiará o valor da ID da Entidade do Portal do LinkedIn explicado posteriormente neste tutorial.

    b. Na caixa de texto **URL de Resposta**, insira o valor da **URL do ACS (Serviço do Consumidor de Declaração)** , você copiará o valor da URL do ACS (Serviço do Consumidor de Declaração) do Portal do LinkedIn explicado posteriormente neste tutorial.

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único em Domínio e URLs do LinkedIn Elevate](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. O aplicativo LinkedIn Elevate espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributos personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo LinkedIn Elevate espera que nameidentifier seja mapeado com **user.mail**. Portanto, é necessário editar o mapeamento de atributo clicando no ícone Editar e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

7. Além do indicado acima, o aplicativo LinkedIn Elevate espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção Declarações de Usuário da caixa de diálogo **Atributos de Usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Atributo de Origem|
    | -------| -------------|
    | department | user.department |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar o LinkedIn Elevate**, copie a URL apropriada de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-linkedin-elevate-single-sign-on"></a>Configurar logon único do LinkedIn Elevate

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do LinkedIn Elevate como um administrador.

1. Em **Centro de Contas**, clique em **Configurações Globais** em **Configurações**. Além disso, selecione **Elevar – Elevar teste do AAD** na lista suspensa.

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Clique em **OU Clique aqui para carregar e copiar campos individuais do formulário** e execute as seguintes etapas:

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Copie a **ID da Entidade** e cole-a na caixa de texto **Identificador** na **Configuração Básica de SAML** no portal do Azure.

    b. Copie a **URL do ACS (Serviço do Consumidor de Declaração)** e cole-a na caixa de texto **URL e Resposta** na **Configuração Básica de SAML** no portal do Azure.

1. Vá para a seção **Configurações de administração do LinkedIn**. Carregue o arquivo XML que você baixou do portal do Azure clicando na opção Carregar arquivo XML.

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique em **Ativar** para habilitar o SSO. O status do SSO será alterado de **Não conectado** para **Conectado**

    ![Configurar o logon único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao LinkedIn Elevate.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **LinkedIn Elevate**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **LinkedIn Elevate**.

    ![O link do LinkedIn Elevate na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-linkedin-elevate-test-user"></a>Criar usuário de teste do LinkedIn Elevate

O aplicativo LinkedIn Elevate dá suporte ao provisionamento de usuário just in time e, após a autenticação, os usuários serão criados automaticamente no aplicativo. Na página de configurações de administração no portal do LinkedIn Elevate, coloque a opção **Atribuir licenças automaticamente** na posição de ativar o provisionamento just in time, o que também atribuirá uma licença ao usuário. O LinkedIn Elevate também dá suporte ao provisionamento automático do usuário. É possível encontrar [aqui](linkedinelevate-provisioning-tutorial.md) detalhes sobre como configurar o provisionamento automático do usuário.

   ![Criação de um usuário de teste do AD do Azure](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do LinkedIn Elevate no Painel de Acesso, deverá ser conectado automaticamente ao LinkedIn Elevate para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
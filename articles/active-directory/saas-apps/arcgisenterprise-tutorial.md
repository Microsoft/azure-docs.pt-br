---
title: 'Tutorial: Integração do Azure Active Directory com o ArcGIS Enterprise | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9642dc67a3707862009efa59d99f0cef54b9b878
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106683"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Tutorial: Integração do Azure Active Directory com o ArcGIS Enterprise

Neste tutorial, você aprenderá a integrar o ArcGIS Enterprise com o Azure AD (Azure Active Directory).
A integração do ArcGIS Enterprise com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao ArcGIS Enterprise.
* Você pode permitir que seus usuários entrem automaticamente no ArcGIS Enterprise (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ArcGIS Enterprise, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do ArcGIS Enterprise habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.



* O ArcGIS Enterprise é compatível com SSO iniciado por **SP e IDP**
* O ArcGIS Enterprise é compatível com o provisionamento de usuário **Just In Time**


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Adicionando o ArcGIS Enterprise da galeria

Para configurar a integração do ArcGIS Enterprise com o Azure AD, você precisa adicionar o ArcGIS Enterprise da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ArcGIS Enterprise da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ArcGIS Enterprise**, selecione **ArcGIS Enterprise** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ArcGIS Enterprise na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o [nome do aplicativo], com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do [nome do aplicativo].

Para configurar e testar o logon único do Azure AD com o [nome do aplicativo], você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do ArcGIS Enterprise](#configure-arcgis-enterprise-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** – para ter um equivalente de Brenda Fernandes no ArcGIS Enterprise vinculado à representação desse usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do AD do Azure com o [nome do aplicativo], execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **ArcGIS Enterprise**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo iniciado por **IDP**:

    ![Informações de domínio e de URLs do ArcGIS Enterprise para logon único](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `<EXTERNAL_DNS_NAME>.portal`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de domínio e de URLs do ArcGIS Enterprise para logon único](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do ArcGIS Enterprise](mailto:support@esri.com) para obter esses valores. Você obterá o valor do identificador na seção **Definir provedor de identidade**, que será explicada mais adiante neste tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Configurar o logon único do ArcGIS Enterprise

1. Em outra janela do navegador da Web, entre no site da empresa do ArcGIS Enterprise como administrador.

2. Selecione **Organização > EDITAR CONFIGURAÇÕES**.

    ![Configuração do ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure1.png)

3. Selecione a guia **Segurança**.

    ![Configuração do ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure2.png)

4. Role para baixo até a seção **Logons corporativos via SAML** e selecione **DEFINIR LOGON CORPORATIVO**.

    ![Configuração do ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure3.png)

5. Na seção **Configurar Provedores de Identidade**, execute as seguintes etapas:

    ![Configuração do ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure4.png)

    a. Forneça um nome como **Teste do Azure Active Directory** na caixa de texto **Nome**.

    b. Na caixa de texto **URL**, cole o valor da **URL de Metadados de Federação do Aplicativo** que você copiou do portal do Azure.

    c. Clique em **Mostrar configurações avançadas**, copie o valor da **ID da Entidade** e cole-o na caixa de texto **Identificador** na seção **Domínio e URLs do ArcGIS Enterprise** no portal do Azure.
    
    ![Configuração do ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure5.png)

    d. Clique em **ATUALIZAR PROVEDOR DE IDENTIDADE**.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a você o acesso ao ArcGIS Enterprise.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **ArcGIS Enterprise**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **ArcGIS Enterprise**.

    ![O link do ArcGIS Enterprise na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-arcgis-enterprise-test-user"></a>Criar um usuário de teste do ArcGIS Enterprise

Nesta seção, um usuário chamado Brenda Fernandes é criado no ArcGIS Enterprise. O ArcGIS Enterprise dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no ArcGIS Enterprise, um novo usuário será criado após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, entre em contato com a  [equipe de suporte do ArcGIS Enterprise](mailto:support@esri.com).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ArcGIS Enterprise no Painel de Acesso, você deverá ser conectado automaticamente ao ArcGIS Enterprise no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


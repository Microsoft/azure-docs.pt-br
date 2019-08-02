---
title: 'Tutorial: Integração do Azure Active Directory ao ArcGIS Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: db56cd7551ef8179aeff575fdd1f2578cbee74ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106703"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integração do Azure Active Directory ao ArcGIS Online

Neste tutorial, você aprende a integrar o ArcGIS Online ao Azure AD (Azure Active Directory).
A integração do ArcGIS Online ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao ArcGIS Online.
* Você pode permitir que seus usuários entrem automaticamente no ArcGIS Online (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ArcGIS Online, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do ArcGIS Online

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ArcGIS Online dá suporte ao SSO iniciado por **SP**

## <a name="adding-arcgis-online-from-the-gallery"></a>Adicionando o ArcGIS Online por meio da galeria

Para configurar a integração do ArcGIS Online ao Azure AD, você precisa adicionar o ArcGIS Online à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o ArcGIS Online por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ArcGIS Online**, selecione **ArcGIS Online** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ArcGIS Online na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o ArcGIS Online, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ArcGIS Online.

Para configurar e testar o logon único do Azure AD com o ArcGIS Online, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do ArcGIS Online](#configure-arcgis-online-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do ArcGIS Online](#create-arcgis-online-test-user)** – para ter um equivalente de Brenda Fernandes no ArcGIS Online que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ArcGIS Online, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **ArcGIS Online**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ArcGIS Online](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.maps.arcgis.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do ArcGIS Online](https://support.esri.com/en/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Para automatizar a configuração no **ArcGIS Online**, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Depois de adicionar a extensão ao navegador, clique em **configurar ArcGIS Online**. Você será direcionado para o aplicativo ArcGIS Online. Lá, forneça as credenciais de administrador para entrar no ArcGIS Online. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas na seção **Configurar o Logon Único do ArcGIS Online**.

### <a name="configure-arcgis-online-single-sign-on"></a>Configurar o Logon Único do ArcGIS Online

1. Se quiser configurar o ArcGIS Online manualmente, abra uma nova janela do navegador da Web, entre no site corporativo do ArcGIS Online como administrador e execute as seguintes etapas:

2. Clique em **EDITAR CONFIGURAÇÕES**.

    ![Editar Configurações](./media/arcgis-tutorial/ic784742.png "Editar Configurações")

3. Clique em **Segurança**.

    ![Segurança](./media/arcgis-tutorial/ic784743.png "Segurança")

4. Em **Logons Corporativos**, clique em **DEFINIR PROVEDOR DE IDENTIDADE**.

    ![Logons Corporativos](./media/arcgis-tutorial/ic784744.png "Logons Corporativos")

5. Na página de configuração **Definir Provedor de Identidade** , realize as seguintes etapas:

    ![Definir Provedor de Identidade](./media/arcgis-tutorial/ic784745.png "Definir Provedor de Identidade")

    a. Na caixa de texto **Nome**, digite o nome de sua organização.

    b. Para **Os metadados do Provedor de Identidade Corporativo serão fornecidos usando**, selecione **Um Arquivo**.

    c. Para carregar seu arquivo de metadados baixado, clique em **Escolher arquivo**.

    d. Clique em **DEFINIR PROVEDOR DE IDENTIDADE**.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao ArcGIS Online.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **ArcGIS Online**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **ArcGIS Online**.

    ![O link do ArcGIS Online na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-arcgis-online-test-user"></a>Criar um usuário de teste do ArcGIS Online

Para permitir que os usuários do Azure AD façam logon no ArcGIS Online, eles devem ser provisionados no ArcGIS Online.  
No caso do ArcGIS Online, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **ArcGIS** .

2. Clique em **CONVIDAR MEMBROS**.
   
    ![Convidar Membros](./media/arcgis-tutorial/ic784747.png "Convidar Membros")

3. Selecione **Adicionar membros automaticamente sem enviar um email** e, depois, clique em **AVANÇAR**.
   
    ![Adicionar Membros Automaticamente](./media/arcgis-tutorial/ic784748.png "Adicionar Membros Automaticamente")

4. Na página do diálogo **Membros** , realize as seguintes etapas:
   
     ![Adicionar e examinar](./media/arcgis-tutorial/ic784749.png "Adicionar e examinar")
    
     a. Insira o **Email**, o **Nome** e o **Sobrenome** de uma conta válida do AAD que deseja provisionar.
  
     b. Clique em **ADICIONAR E EXAMINAR**.
5. Examine os dados inseridos e, depois, clique em **ADICIONAR MEMBROS**.
   
    ![Adicionar membro](./media/arcgis-tutorial/ic784750.png "Adicionar membro")
        
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ArcGIS Online no Painel de Acesso, você deverá ser conectado automaticamente ao ArcGIS Online no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Tutorial: Integração do Azure Active Directory ao OpenAthens | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 544c74dc5bd336ab79593fc081b4e48ac0caf3ac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095538"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Tutorial: Integração do Azure Active Directory ao OpenAthens

Neste tutorial, você aprende a integrar o OpenAthens ao Azure AD (Azure Active Directory).
A integração do OpenAthens ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao OpenAthens.
* Você pode permitir que os usuários sejam conectados automaticamente ao OpenAthens (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o OpenAthens, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do OpenAthens

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O OpenAthens dá suporte ao SSO iniciado por **IDP**

* O OpenAthens dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-openathens-from-the-gallery"></a>Adicionar o OpenAthens da galeria

Para configurar a integração do OpenAthens ao Azure AD, você precisará adicionar o OpenAthens da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OpenAthens da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **OpenAthens**, selecione **OpenAthens** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![OpenAthens na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o OpenAthens, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do OpenAthens.

Para configurar e testar o logon único do Azure AD com o OpenAthens, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do OpenAthens](#configure-openathens-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do OpenAthens](#create-openathens-test-user)** – para ter um equivalente de Brenda Fernandes no OpenAthens que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o OpenAthens, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **OpenAthens**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

5. Na seção **Configuração Básica do SAML**, carregue o **Arquivo de metadados do Provedor de Serviços**, cujas etapas são mencionadas mais adiante neste tutorial.

    a. Clique em **Carregar arquivo de metadados**.

    ![carregar metadados do OpenAthens](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Procurar e carregar metadados do OpenAthens](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, o valor de **Identificador** será preenchido automaticamente na caixa de texto da seção **Configuração Básica do SAML**:

    ![Informações de logon único de Domínio e URLs do OpenAthens](common/idp-identifier.png)

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Configurar o Logon Único do OpenAthens

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do OpenAthens como administrador.

2. Selecione **Conexões** na lista na guia **Gerenciamento**. 

    ![Configurar o logon único](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Selecione **SAML 1.1/2.0**e, em seguida, selecione o botão **Configurar**.

    ![Configurar o logon único](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Para adicionar a configuração, selecione o botão **Procurar** para carregar o arquivo .xml de metadados que você baixou do portal do Azure e, em seguida, selecione **Adicionar**.

    ![Configurar o logon único](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Execute as seguintes etapas na guia **Detalhes**.

    ![Configurar o logon único](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Em **Mapeamento do nome de exibição**, selecione **Usar atributo**.

    b. Na caixa de texto **Exibir atributo do nome**, insira o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. Em **Mapeamento de usuário exclusivo**, selecione **Usar atributo**.

    d. Na caixa de texto **Atributo de usuário exclusivo**, insira o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Em **Status**, marque todas as três caixas de seleção.

    f. Em **Criar contas locais**, selecione **automaticamente**.

    g. Selecione **Salvar alterações**.

    h. Na guia **</> Terceira Parte Confiável**, copie a **URL de Metadados** e abra isso no navegador para baixar o arquivo **XML de metadados do SP**. Carregue esse arquivo de metadados do SP na seção **Configuração Básica do SAML** no Azure AD.

    ![Configurar o logon único](./media/openathens-tutorial/tutorial_openathens_application5.png)

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao OpenAthens.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **OpenAthens**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **OpenAthens**.

    ![O link do OpenAthens na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-openathens-test-user"></a>Criar um usuário de teste do OpenAthens

Nesta seção, um usuário chamado Brenda Fernandes será criado no OpenAthens. O OpenAthens dá suporte ao **provisionamento de usuário Just-In-Time**, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no OpenAthens, um novo será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do OpenAthens no Painel de Acesso, você deverá ser conectado automaticamente ao OpenAthens, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


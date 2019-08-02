---
title: 'Tutorial: Integração do Azure Active Directory com o Hosted Graphite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Hosted Graphite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79a83ec34d8e9d577e6bde8421a11b4c7bf6b241
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100903"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Integração do Azure Active Directory com o Hosted Graphite

Neste tutorial, você aprenderá a integrar o Hosted Graphite ao Azure AD (Azure Active Directory).
A integração do Hosted Graphite ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Hosted Graphite.
* Você pode permitir que seus usuários entrem automaticamente no Hosted Graphite (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Hosted Graphite, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Hosted Graphite habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Hosted Graphite dá suporte para SSO iniciado por **SP e IDP**
* O Hosted Graphite é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-hosted-graphite-from-the-gallery"></a>Adição do Hosted Graphite por meio da Galeria

Para configurar a integração do Hosted Graphite ao Azure AD, você precisa adicionar o Hosted Graphite da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Hosted Graphite por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Hosted Graphite**, selecione **Hosted Graphite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Hosted Graphite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Hosted Graphite com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Hosted Graphite.

Para configurar e testar o logon único do Azure AD com o Hosted Graphite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Hosted Graphite](#configure-hosted-graphite-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste no Hosted Graphite](#create-hosted-graphite-test-user)** – para ter um equivalente de Brenda Fernandes no Hosted Graphite que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Hosted Graphite, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Hosted Graphite**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de URLs e Domínio do Graphite Hospedado](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.hostedgraphite.com/metadata/<user id>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de URLs e Domínio do Graphite Hospedado](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Para obter esses valores, você pode ir para Acessar-> Configuração do SAML no lado do Aplicativo ou entrar em contato com a [equipe de suporte do Hosted Graphite](mailto:help@hostedgraphite.com).

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar Hosted Graphite**, copie a URL apropriada de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-hosted-graphite-single-sign-on"></a>Configurar logon único do Hosted Graphite

1. Faça logon no seu locatário do Hosted Graphite como administrador.

2. Acesse a **página de configuração do SAML** na barra lateral (**Acesso -> Configuração do SAML**).

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Confirmar esses URls correspondem à configuração feita na seção **Configuração de SAML Básica** do portal do Azure.

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. Nas caixas de texto **ID da Entidade ou do Emissor** e **URL de Logon SSO**, cole o valor do **Identificador do Azure AD** e da **URL de Logon** que você copiou no portal do Azure.

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Selecione **Somente leitura** como a **Função de usuário padrão**.

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Abra seu certificado codificado em base 64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**.

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Clique no botão **Salvar** .

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

Nesta seção, você habilita Brenda Fernandes a usar o logon único do Azure concedendo acesso ao Hosted Graphite.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **Hosted Graphite**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Hosted Graphite**.

    ![O link do Hosted Graphite na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-hosted-graphite-test-user"></a>Criar usuário de teste no Hosted Graphite

Nesta seção, um usuário chamado Brenda Fernandes será criado no Hosted Graphite. O Hosted Graphite dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Hosted Graphite, um novo será criado após a autenticação.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Hosted Graphite via <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Hosted Graphite no Painel de Acesso, você deverá ser conectado automaticamente ao Hosted Graphite no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


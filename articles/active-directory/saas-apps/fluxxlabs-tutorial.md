---
title: 'Tutorial: Integração do Azure Active Directory ao Fluxx Labs | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102383"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Tutorial: Integração do Azure Active Directory com o Fluxx Labs

Neste tutorial, você aprenderá a integrar o Fluxx Labs ao Azure AD (Azure Active Directory).
A integração do Fluxx Labs ao Microsoft Azure Active Directory oferece os seguintes benefícios:

* No Microsoft Azure Active Directory, é possível controlar quem tem acesso ao Fluxx Labs.
* Você pode permitir que os usuários sejam conectados automaticamente ao Fluxx Labs (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com o Fluxx Labs, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Fluxx Labs

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Fluxx Labs dá suporte ao SSO iniciado por **IdP**

## <a name="adding-fluxx-labs-from-the-gallery"></a>Adição do Fluxx Labs da galeria

Para configurar a integração do Fluxx Labs ao Microsoft Azure Active Directory, você precisará adicionar o Fluxx Labs da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Fluxx Labs da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Fluxx Labs**, selecione **Fluxx Labs** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Fluxx Labs na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Fluxx Labs, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Fluxx Labs.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o Fluxx Labs, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Fluxx Labs](#configure-fluxx-labs-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Fluxx Labs](#create-fluxx-labs-test-user)** – para ter um equivalente de Brenda Fernandes no Fluxx Labs que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Fluxx Labs, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Fluxx Labs**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Fluxx Labs](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | Ambiente | Padrão de URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    | Ambiente | Padrão de URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do Fluxx Labs](mailto:travis@fluxxlabs.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Fluxx Labs**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-fluxx-labs-single-sign-on"></a>Configurar o logon único do Fluxx Labs

1. Em outra janela do navegador da Web, entre em seu site de empresa do Fluxx Labs como administrador.

2. Selecione **Administrador** abaixo da seção **Configurações**.

    ![Configuração do Fluxx Labs](./media/fluxxlabs-tutorial/config1.png)

3. No Painel de Administração, selecione **Plug-ins** > **Integrações** e, em seguida, selecione **SSO de SAML (Desabilitado)**

    ![Configuração do Fluxx Labs](./media/fluxxlabs-tutorial/config2.png)

4. Na seção de atributos, realize as seguintes etapas:

    ![Configuração do Fluxx Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Marque a caixa de seleção **SSO do SAML**.

    b. Na caixa de texto **Caminho de solicitação**, digite **/auth/saml**.

    c. Na caixa de texto **Caminho de retorno de chamada**, digite **/auth/saml/callback**.

    d. Na caixa de texto **URL do Serviço do Consumidor de Declaração (URL de Logon Único)** , insira o valor da **URL de Resposta** que você inseriu no portal do Azure.

    e. Na caixa de texto **Público (ID da Entidade SP)** , insira o valor de **Identificador**, que você inseriu no portal do Azure.

    f. Na caixa de texto **URL de Destino de SSO do Provedor de Identidade**, cole o valor da **URL de Logon** copiado do portal do Azure.

    g. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de Identidade**.

    h. Na caixa de texto **Formato do identificador de nome**, insira o valor `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Clique em **Save** (Salvar).

    > [!NOTE]
    > Depois que o conteúdo for salvado, o campo aparecerá em branco por questões de segurança, mas o valor foi salvo na configuração.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Fluxx Labs.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Fluxx Labs**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Fluxx Labs**.

    ![O link do Fluxx Labs na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-fluxx-labs-test-user"></a>Criar um usuário de teste do Fluxx Labs

Para permitir que os usuários do Azure AD entrem no Fluxx Labs, eles precisam ser provisionados no Fluxx Labs. No caso do Fluxx Labs, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do Fluxx Labs como administrador.

2. Clique no **ícone** exibido abaixo.

    ![Configuração do Fluxx Labs](./media/fluxxlabs-tutorial/config6.png)

3. No painel, clique no ícone exibido abaixo para abrir o cartão **Novas PESSOAS**.

    ![Configuração do Fluxx Labs](./media/fluxxlabs-tutorial/config4.png)

4. Na seção **NOVAS PESSOAS**, execute as etapas a seguir:

    ![Configuração do Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. O Fluxx Labs usa email como identificador exclusivo para logons únicos. Preencha o campo **UID do SSO** com o endereço de email do usuário, que corresponde ao endereço de email, que estão usando como logon com o SSO.

    b. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Fluxx Labs no Painel de Acesso, você deverá ser conectado automaticamente ao Fluxx Labs, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


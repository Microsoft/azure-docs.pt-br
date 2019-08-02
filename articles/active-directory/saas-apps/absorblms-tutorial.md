---
title: 'Tutorial: Integração do Azure Active Directory ao Absorb LMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Absorb LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 936de76d1117c56f5a9dec48b51f33b9afa15351
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107512"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integração do Azure Active Directory ao Absorb LMS

Neste tutorial, você aprenderá a integrar o Absorb LMS ao Azure AD (Azure Active Directory).
A integração do Absorb LMS ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Absorb LMS.
* Você pode permitir que seus usuários entrem automaticamente no Absorb LMS (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Absorb LMS, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura do Absorb LMS com logon único habilitado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Absorb LMS é compatível com SSO iniciado por **IDP**

## <a name="adding-absorb-lms-from-the-gallery"></a>Adicionar o Absorb LMS da Galeria

Para configurar a integração do Absorb LMS ao Azure AD, você precisa adicionar o Absorb LMS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Absorb LMS da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Absorb LMS**, selecione **Absorb LMS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Absorb LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Absorb LMS, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Absorb LMS.

Para configurar e testar o logon único do Azure AD com o Absorb LMS, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Absorb LMS](#configure-absorb-lms-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Absorb LMS](#create-absorb-lms-test-user)** – para ter um equivalente de Brenda Fernandes no Absorb LMS que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Absorb LMS, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Absorb LMS**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Definir logon único com SAML**, clique no botão **Editar** para abrir o diálogo **Configuração básica de SAML**.

    ![Informações de logon único de Domínio e URLs do Absorb LMS](common/idp-intiated.png)

    Se você estiver usando **Absorb 5 – interface do usuário**, use a seguinte configuração:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://company.myabsorb.com/account/saml`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://company.myabsorb.com/account/saml`

    Se você estiver usando **Absorb 5 – Nova Experiência de Aprendiz**, use a seguinte configuração:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte do cliente Absorb LMS](https://support.absorblms.com/hc/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

7. Na seção **Configurar o Absorb LMS**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-absorb-lms-single-sign-on"></a>Configurar logon único do Absorb LMS

1. Em uma nova janela do navegador da Web, entre no site da empresa do Absorb LMS como administrador.

2. Selecione o botão **Conta** na parte superior direita.

    ![Botão Conta](./media/absorblms-tutorial/1.png)

3. No painel Conta, selecione **Configurações do Portal**.

    ![Link de Configurações do Portal](./media/absorblms-tutorial/2.png)

4. Selecione a guia **Gerenciar Configurações de SSO**.

    ![A guia Usuários](./media/absorblms-tutorial/managesso.png)

5. Na página **Gerenciar Configurações de Logon Único**, faça o seguinte:

    ![Página de configuração de logon único](./media/absorblms-tutorial/settings.png)

    a. Na caixa de texto **Nome**, insira o nome como o SSO do Azure AD Marketplace.

    b. Selecione **SAML** como um **Método**.

    c. No Bloco de Notas, abra o certificado que você baixou pelo Portal do Azure. Remoa as marcas **---BEGIN CERTIFICATE---** e **---END CERTIFICATE---** . Em seguida, na caixa **Chave**, cole o conteúdo restante.

    d. Na caixa **Modo**, selecione **Provedor de Identidade Iniciado**.

    e. Na caixa **Propriedade de ID**, selecione o atributo que você configurou como o identificador de usuário no Azure AD. Por exemplo, se *nameidentifier* estiver selecionado no Azure AD, selecione **Nome de Usuário**.

    f. Selecione **Sha256** como um **Tipo de Assinatura**.

    g. Na caixa **URL de Logon**, cole a **URL de Acesso do Usuário** a partir da página **Propriedade** do aplicativo do Portal do Azure.

    h. Na **URL de Logoff**, cole o valor da **URL de Saída** copiado da janela **Configurar logon** do Portal do Azure.

    i. Defina a opção **Redirecionar Automaticamente** como **Ativo**.

6. Selecione **Salvar**.

    ![Alternância para Permitir apenas Logon SSO](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon\@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você concederá acesso ao Absorb LMS a Brenda Fernandes para habilitá-la a usar o logon único do Azure.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Absorb LMS**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Absorb LMS**.

    ![O link do Absorb LMS na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-absorb-lms-test-user"></a>Criar um usuário de teste do Absorb LMS

Para que os usuários do Azure AD entrem no Absorb LMS, os usuários deverão ser configurados no Absorb LMS. No caso do Absorb LMS, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Entre no site da empresa do Absorb LMS como administrador.

2. No painel **Usuários**, selecione **Usuários**.

    ![Link Usuários](./media/absorblms-tutorial/absorblms_userssub.png)

3. Selecione a guia **Usuário**.

    ![Lista suspensa Adicionar Novo](./media/absorblms-tutorial/absorblms_createuser.png)

4. Na página **Adicionar Usuário**, faça o seguinte:

    ![Página Adicionar Usuário](./media/absorblms-tutorial/user.png)

    a. Na caixa **Nome**, digite o primeiro nome, como **Brenda**.

    b. Na caixa **Sobrenome**, digite o último nome, como **Fernandes**.

    c. Na caixa **Nome de Usuário**, digite o nome completo, como **Brenda Fernandes**.

    d. Na caixa **Senha**, digite a senha do usuário.

    e. Na caixa **Confirmar Senha**, digite a senha novamente.

    f. Defina a alternância **Está Ativo** para **Ativo**.

5. Selecione **Salvar**.

    ![Alternância para Permitir apenas Logon SSO](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Por padrão, o provisionamento de usuários não está habilitado no SSO. Se o cliente deseja habilitar esse recurso, ele precisa defini-lo conforme mencionado [nesta](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentação. Observe também que o provisionamento de usuários só está disponível em **Absorb 5 – Nova Experiência de Aprendiz** com a URL do ACS – `https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Absorb LMS no Painel de Acesso, você deverá ser conectado automaticamente ao Absorb LMS no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

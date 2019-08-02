---
title: 'Tutorial: Integração do Azure Active Directory ao Andromeda | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68fa570ecfbafe2000bfa6eb9fa159dff48219a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107080"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Tutorial: Integração do Azure Active Directory ao Andromeda

Neste tutorial, você aprende a integrar o Andromeda ao Azure AD (Azure Active Directory).
A integração do Andromeda ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Andromeda.
* Você pode permitir que seus usuários entrem automaticamente no Andromeda (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Andromeda, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Andromeda

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Andromeda dá suporte ao SSO iniciado por **SP e IDP**
* O Andromeda é compatível com o provisionamento de usuário **Just In Time**

## <a name="adding-andromeda-from-the-gallery"></a>Adicionando o Andromeda por meio da galeria

Para configurar a integração do Andromeda ao Azure AD, você precisa adicionar o Andromeda por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o Andromeda por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Andromeda**, selecione **Andromeda** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Andromeda na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Andromeda, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Andromeda.

Para configurar e testar o logon único do Azure AD com o Andromeda, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Andromeda](#configure-andromeda-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Andromeda](#create-andromeda-test-user)** – para ter um equivalente de Brenda Fernandes no Andromeda que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Andromeda, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Andromeda**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Andromeda](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do Andromeda](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Esses valores não são reais. Você atualizará o valor com o identificador real, URL de resposta, e URL de logon que é explicado no tutorial posteriormente.

6. O aplicativo Andromeda espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

    > [!Important]
    > Desmarque out as definições do NameSpace ao configurá-los.

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas: 

    | NOME | Atributo de Origem|
    | ------ | -----------|
    | função        | Função específica do aplicativo |
    | Tipo        | Tipo de Aplicativo |
    | company       | CompanyName |

    > [!NOTE]
    > Não há valores reais. Esses valores são somente para finalidade de demonstração; use as funções de sua organização.

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

8. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

9. Na seção **Configurar o Andromeda**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-andromeda-single-sign-on"></a>Configurar o logon único do Andromeda

1. Faça logon no site de empresa do Andromeda como administrador.

2. Na parte superior da barra de menus, clique em **Admin** e navegue até **Administração**.

    ![Administrador do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. No lado esquerdo da barra de ferramentas na seção **Interfaces**, clique em **Configuração do SAML**.

    ![SAML do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Na página **Configuração do SAML**, realize as seguintes etapas:

    ![Configuração do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Marque **Habilitar o SSO com SAML**.

    b. Na seção **Informações sobre o Andromeda**, copie o valor **Identidade do SP** e cole-o na caixa de texto **Identificador** da seção **Configuração básica do SAML**.

    c. Copie o valor **URL do Consumidor** e cole-o na caixa de texto **URL de Resposta** da seção **Configuração básica do SAML**.

    d. Copie o valor **URL de Logon** e cole-o na caixa de texto **URL de Logon** da seção **Configuração básica do SAML**.

    e. Na seção **provedor de identidade SAML**, digite seu nome IDP.

    f. Na caixa de texto **Ponto de extremidade do logon único**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    g. Abra o **certificado codificado com Base64** baixado do portal do Azure no bloco de notas, cole-o na caixa de texto **Certificado X 509**.
    
    h. Mapeie os seguintes atributos com o respectivo valor para facilitar o logon SSO do Azure AD. O atributo **ID de usuário** é necessário para fazer login. Para o provisionamento, **Email**, **Company**, **UserType** e **Role** são obrigatórios. Nesta seção, definimos o mapeamento de atributos (nome e valores) correlacionados àqueles definidos no portal do Azure

    ![attbmap do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Clique em **Save** (Salvar).

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Andromeda.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Andromeda**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Andromeda**.

    ![O link do Andromeda na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-andromeda-test-user"></a>Criar um usuário de teste do Andromeda

Nesta seção, um usuário chamado Brenda Fernandes será criado no Andromeda. O Andromeda dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Andromeda, um novo usuário será criado após a autenticação. Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Andromeda](https://www.ngcsoftware.com/support/).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Andromeda no Painel de Acesso, você deverá ser conectado automaticamente ao Andromeda no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
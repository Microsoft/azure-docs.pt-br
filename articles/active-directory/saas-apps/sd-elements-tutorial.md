---
title: 'Tutorial: Integração do Azure Active Directory com o SD Elements | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o SD Elements.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 9e6deb8b31559b8c5add06f1771cd69df0c1f136
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091359"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Tutorial: Integração do Azure Active Directory com o SD Elements

Neste tutorial, você aprende a integrar o SD Elements ao Azure AD (Azure Active Directory).
A integração do SD Elements ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao SD Elements.
* Você pode permitir que os usuários entrem automaticamente no SD Elements (logon único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure a SD Elements, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do SD Elements habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SD Elements é compatível com o SSO iniciado por **IDP**

## <a name="adding-sd-elements-from-the-gallery"></a>Adicionando elementos SD da galeria

Para configurar a integração de SD Elements ao Azure AD, você precisa adicionar SD Elements da galeria à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar SD Elements da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SD Elements**, selecione **SD Elements** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SD Elements na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SD Elements usando um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SD Elements.

Para configurar e testar o logon único do Azure AD com o SD Elements, é preciso concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do SD Elements](#configure-sd-elements-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SD Elements](#create-sd-elements-test-user)** – para que haja um equivalente de Brenda Fernandes no SD Elements que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SD Elements, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SD Elements**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    ![Informações de domínio e de URLs do SD Elements para logon único](common/idp-intiated.png)

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do SD Elements](mailto:support@sdelements.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo SD Elements espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário** .

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:

    | NOME |  Atributo de Origem|
    | --- | --- |
    | email |user.mail |
    | nome |user.givenname |
    | sobrenome |user.surname |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

8. Na seção **Configurar o SD Elements**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-sd-elements-single-sign-on"></a>Configurar o logon único do SD Elements

1. Para habilitar logon único, entre em contato com a [equipe de suporte do SD Elements](mailto:support@sdelements.com) e forneça a ela o arquivo de certificado baixado.

1. Em outra janela do navegador, faça logon no locatário do SD Elements como administrador.

1. No menu na parte superior, clique em **Sistema** e, depois, em **Logon Único**.

    ![Configurar o logon único](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Na caixa de diálogo **Configurações de Logon Único** , execute as seguintes etapas:

    ![Configurar o logon único](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Como **Tipo de SSO**, selecione **SAML**.

    b. Na caixa de texto **ID da Entidade do Provedor de Identidade**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.

    c. Na caixa de texto **Serviço de Logon Único do Provedor de Identidade**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    d. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SD Elements.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **SD Elements**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SD Elements**.

    ![O link do SD Elements na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sd-elements-test-user"></a>Criar um usuário de teste do SD Elements

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no SD Elements. No caso de SD Elements, criar usuários do SD Elements é uma tarefa manual.

**Para criar Brenda Fernandes no SD Elements, execute as seguintes etapas:**

1. Em uma janela de navegador da web, faça logon no site SD Elements da sua empresa como administrador.

1. No menu na parte superior, clique em **Gerenciamento de Usuários** e, depois, em **Usuários**.

    ![Criar um usuário de teste de elementos de SD](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Clique em **Adicionar Novo Usuário**.

    ![Criar um usuário de teste de elementos de SD](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Na caixa de diálogo **Adicionar Novo Usuário**, realize as seguintes etapas:

    ![Criar um usuário de teste de elementos de SD](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Na caixa de texto **Email**, insira o email do usuário, como **brittasimon@contoso.com** .

    b. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    c. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    d. Como **Função**, selecione **Usuário**.

    e. Clique em **Criar Usuário**.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SD Elements no Painel de Acesso, você será conectado automaticamente ao aplicativo SD Elements para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
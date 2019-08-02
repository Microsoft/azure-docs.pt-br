---
title: 'Tutorial: Integração do Azure Active Directory com o Spotinst | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 817eecce6c2c1be273b47494e8ecc14180394796
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705347"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>Tutorial: Integração do Azure Active Directory com o Spotinst

Neste tutorial, você aprenderá como integrar o Spotinst ao Azure AD (Azure Active Directory).
A integração do Spotinst ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no AD do Azure quem tem acesso ao Spotinst.
* Você pode permitir que seus usuários entrem automaticamente no Spotinst (logon único) com suas contas do Microsoft Azure Active Directory.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Spotinst, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura habilitada para logon único do Spotinst

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Spotinst dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-spotinst-from-the-gallery"></a>Adicionando Spotinst da Galeria

Para configurar a integração do Spotinst no Azure AD, você precisa adicionar o Spotinst da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Spotinst da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Spotinst**, selecione **Spotinst** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Spotinst na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o Spotinst, com base em um usuário de teste chamado **Britta Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado do Spotinst.

Para configurar e testar o AD do Azure logon único com Spotinst, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Spotinst](#configure-spotinst-single-sign-on)** : para configurar o logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Spotinst](#create-spotinst-test-user)** : para ter um equivalente de Brenda Fernandes no Spotinst que esteja vinculado à representação de usuário no Microsoft Azure Active Directory.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Microsoft Azure Active Directory com o Spotinst, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **Spotinst**, clique em **Logon Único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Informações de logon único Spotinst domínio e URLs](common/idp-preintegrated-relay.png)

    a. Clique em **Definir URLs adicionais**.

    b. No **estado de retransmissão** caixa de texto, digite um valor: `<ID>`

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único Spotinst domínio e URLs](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://console.spotinst.com/auth/saml`

    > [!NOTE]
    > O valor de estado de retransmissão não é real. Você atualizará o valor do estado do relé com o valor atual do estado do relé, que será explicado posteriormente no tutorial.

6. Aplicativo Spotinst espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas: 

    | NOME | Atributo de Origem|
    | -----| --------------- |
    | Email | user.mail |
    | Nome | user.givenname |
    | Sobrenome | user.surname |

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

9. Na seção **Configurar o Spotinst**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-spotinst-single-sign-on"></a>Configurar o logon único do Spotinst

1. Em uma janela diferente do navegador da Web, faça logon no Spotinst como administrador de segurança.

2. Clique no **ícone do usuário** no canto superior direito da tela e clique **configurações**.

    ![Configurações de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Clique no **segurança** na parte superior e, em seguida, selecione **provedores de identidade** e execute as seguintes etapas:

    ![Segurança Spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Copie o valor **Estado de Retransmissão** da sua instância e cole-o na caixa de texto **Estado de Retransmissão** na seção **Configuração Básica de SAML** no portal do Azure.

    b. Clique em **PROCURAR** para carregar o arquivo xml de metadados que você baixou do portal do Azure

    c. Clique em **SALVAR**.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Spotinst.

1. No portal do Azure, escolha **Aplicativos Empresariais**, escolha **Todos os Aplicativos** e, em seguida, escolha **Spotinst**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Spotinst**.

    ![O link Spotinst na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-spotinst-test-user"></a>Criar um usuário de teste do Spotinst

O objetivo desta seção é criar um usuário chamado Britta Simon no Spotinst.

1. Se você configurou o aplicativo no modo iniciado pelo **SP**, execute as seguintes etapas:

   a. Em uma janela diferente do navegador da Web, faça logon no Spotinst como administrador de segurança.

   b. Clique no **ícone do usuário** no canto superior direito da tela e clique **configurações**.

    ![Configurações de Spotinst](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Clique em **os usuários** e selecione **adicionar usuário**.

    ![Configurações de Spotinst](./media/spotinst-tutorial/adduser1.png)

    d. Na seção add user, execute as seguintes etapas:

    ![Configurações de Spotinst](./media/spotinst-tutorial/adduser2.png)

    * Na caixa de texto **Nome Completo**, digite o nome completo do usuário como **BrittaSimon**.

    * Na caixa de texto **Email**, digite o endereço de email do usuário, como `brittasimon\@contoso.com`.

    * Selecione seus detalhes específicos da organização para a **Função da organização, Função da conta e Contas**.

2. Se você configurou o aplicativo no modo iniciado **IDP**, não há um item de ação nesta seção. Spotinst dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Um novo usuário é criado durante uma tentativa de acessar o Spotinst, se ele ainda não existir.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Spotinst no Painel de Acesso, você deve ser conectado automaticamente ao Spotinst no qual configurou o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


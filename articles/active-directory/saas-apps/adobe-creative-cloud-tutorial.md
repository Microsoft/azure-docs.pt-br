---
title: 'Tutorial: Integração do Azure Active Directory ao Adobe Creative Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9650389d8ec641af1db0652dd09fbd7f06223956
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107345"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Tutorial: Integração do Azure Active Directory ao Adobe Creative Cloud

Neste tutorial, você aprenderá a integrar a Adobe Creative Cloud ao Azure AD (Azure Active Directory).
A integração da Adobe Creative Cloud ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso à Adobe Creative Cloud.
* É possível permitir que seus usuários façam logon automaticamente no Adobe Creative Cloud (Logon Único) com as respectivas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a Adobe Creative Cloud, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Adobe Creative Cloud habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Adobe Creative Cloud dá suporte ao SSO iniciado por **SP**

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adicionando o Adobe Creative Cloud da Galeria

Para configurar a integração da Adobe Creative Cloud com o Azure AD, será necessário adicionar a Creative Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a Adobe Creative Cloud da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Adobe Creative Cloud**, selecione **Adobe Creative Cloud** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adobe Creative Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Adobe Creative Cloud, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adobe Creative Cloud.

Para configurar e testar o logon único do Azure AD com a Adobe Creative Cloud, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Adobe Creative Cloud](#configure-adobe-creative-cloud-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** – para ter um equivalente de Brenda Fernandes no Adobe Creative Cloud que esteja vinculado à representação da usuária no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Adobe Creative Cloud, siga as etapas abaixo:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Adobe Creative Cloud**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs da Adobe Creative Cloud](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite o valor como: `https://adobe.com`.

    b. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize esse valor com o Identificador real. Contate a [equipe de suporte ao cliente do Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) para obter esse valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo Adobe Creative Cloud espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | NOME | Atributo de Origem|
    |----- | --------- |
    | Nome | user.givenname |
    | Sobrenome | user.surname |
    | Email | user.mail

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

    > [!NOTE]
    > Os usuários precisam ter uma licença válida do Office 365 ExO para valor de declaração de email a ser preenchida na resposta SAML.

7. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

8. Na seção **Configurar o Adobe Creative Cloud**, copie as URLs apropriadas de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Configurar o logon único do Adobe Creative Cloud

1. Em outra janela do navegador da Web, entre no [Console de Administração do Adobe](https://adminconsole.adobe.com) como administrador.

2. Acesse **Configurações** na barra de navegação superior e, em seguida, escolha **Identidade**. A lista de domínios será aberta. Clique no link **Configurar** no domínio. Em seguida, siga as etapas na seção **Configuração do logon único necessária**. Para obter mais informações, consulte [Configurar um domínio](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Configurações](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Configurações")

    a. Clique em **Procurar** para carregar o certificado baixado do Azure AD para o **Certificado do IdP**.

    b. Na caixa de texto **Emissor IdP**, coloque o valor da **ID da entidade SAML** que você copiou da seção **Configurar logon** no portal do Azure.

    c. Na caixa de texto **URL de logon IdP**, coloque o valor da **URL de serviço do SAML SSO** que você copiou da seção **Configurar logon** no portal do Azure.

    d. Selecione **Redirecionamento HTTP** como **Associação IdP**.

    e. Selecione **Endereço de email** como **Configuração de logon do usuário**.

    f. Clique no botão **Salvar** .

3. Agra o painel apresentará o arquivo XML **"Baixar metadados"** . Ele contém a URL EntityDescriptor e a URL AssertionConsumerService da Adobe. Abra o arquivo e configure-os no aplicativo Azure AD.

    ![Configurar o logon único no lado do aplicativo](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Use o valor EntityDescriptor que a Adobe forneceu a você para **Identificador** na caixa de diálogo **Definir configurações de aplicativo**.

    b. Use o valor AssertionConsumerService que a Adobe forneceu a você para **URL de resposta** na caixa de diálogo **Definir configurações de aplicativo**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo acesso à Adobe Creative Cloud.

1. No portal do Azure, escolha **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Adobe Creative Cloud**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Adobe Creative Cloud**.

    ![O link da Adobe Creative Cloud na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-adobe-creative-cloud-test-user"></a>Criar um usuário de teste da Adobe Creative Cloud

Para que os usuários do Azure AD façam logon na Adobe Creative Cloud, eles devem ser provisionados na Adobe Creative Cloud. No caso da Adobe Creative Cloud, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1. Entre no site [Console de Administração do Adobe](https://adminconsole.adobe.com) como administrador.

2. Adicione o usuário no console da Adobe como uma ID Federada e atribua-o a um Perfil de Produto. Para obter informações detalhadas sobre como adicionar usuários, consulte [Adicionar usuários no Console de Administração do Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Neste ponto, digite seu endereço de email/upn no formulário signin da Adobe, pressione tab e você deve ser federado de volta ao Azure AD:
   * Acesso via Web: www\.adobe.com > sign-in
   * No utilitário de aplicativo de área de trabalho > entrar
   * No aplicativo > ajuda > entrar

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Adobe Creative Cloud no Painel de Acesso, você deverá ser conectado automaticamente ao Adobe Creative Cloud no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Configurar um domínio (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Configurar o Azure para uso com o SSO do Adobe (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

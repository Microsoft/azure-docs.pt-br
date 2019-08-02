---
title: 'Tutorial: Integração do Azure Active Directory ao OfficeSpace Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o OfficeSpace Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: cf40686adcee757db065fa6f68f990ea122b9747
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095857"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Tutorial: Integração do Active Directory do Azure ao OfficeSpace Software

Neste tutorial, você aprende a integrar o OfficeSpace Software ao Azure AD (Azure Active Directory).
A integração do OfficeSpace Software ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao OfficeSpace Software.
* Você pode permitir que os usuários sejam conectados automaticamente no OfficeSpace Software (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao OfficeSpace Software, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do OfficeSpace Software habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O OfficeSpace Software dá suporte a SSO iniciado por **SP**

* O OfficeSpace Software dá suporte a provisionamento de usuário **Just-In-Time**

## <a name="adding-officespace-software-from-the-gallery"></a>Adicionando o OfficeSpace Software por meio da galeria

Para configurar a integração do OfficeSpace Software no Azure AD, é necessário adicionar o OfficeSpace Software por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o OfficeSpace Software por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **OfficeSpace Software**, selecione **OfficeSpace Software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![OfficeSpace Software na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com OfficeSpace Software com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do OfficeSpace Software.

Para configurar e testar o logon único do Azure AD com o OfficeSpace Software, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do OfficeSpace Software](#configure-officespace-software-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do OfficeSpace Software](#create-officespace-software-test-user)** – para ter um equivalente de Brenda Fernandes no OfficeSpace Software que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o OfficeSpace Software, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **OfficeSpace Software**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do OfficeSpace Software](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do OfficeSpace Software](mailto:support@officespacesoftware.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. O aplicativo OfficeSpace Software espera declarações SAML em um formato específico, que exige a inclusão de mapeamentos de atributos personalizados na configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo OfficeSpace Software espera que o **nameidentifier** seja mapeado com **user.mail**, portanto, é necessário editar o mapeamento de atributos, clicando no ícone **Editar** e alterar o mapeamento de atributos.

    ![image](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo OfficeSpace Software espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Atributo de Origem|
    | ---------------| --------------- |
    | email | user.mail |
    | Nome | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

7. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

8. Na seção **Certificado de Autenticação SAML**, copie a **Impressão Digital** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

9. Na seção **Configurar OfficeSpace Software**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-officespace-software-single-sign-on"></a>Configurar o logon único do OfficeSpace Software

1. Em outra janela do navegador da Web, faça logon no locatário do OfficeSpace Software como administrador.

2. Acesse **Configurações** e clique em **Conectores**.

    ![Configurar o logon único no lado do aplicativo](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Clique em **Autenticação SAML**.

    ![Configurar o logon único no lado do aplicativo](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Na seção **Autenticação SAML** , realize as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Na caixa de texto **URL do provedor de logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    b. Na caixa de texto **URL de destino do idp do cliente**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Cole o valor da **Impressão digital** copiado do Portal do Azure na caixa de texto **Impressão digital de certificado de IdP do cliente**. 

    d. Clique em **Salvar Configurações**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao OfficeSpace Software.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **OfficeSpace Software**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **OfficeSpace Software**.

    ![O link do OfficeSpace Software na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-officespace-software-test-user"></a>Criar usuário de teste do OfficeSpace Software

Nesta seção, um usuário chamado Brenda Fernandes é criado no OfficeSpace Software. O OfficeSpace Software dá suporte para provisionamento de usuário just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no OfficeSpace Software, um novo será criado após a autenticação.

> [!NOTE]
> Se precisar criar um usuário manualmente, será necessário contatar a [equipe de suporte do OfficeSpace Software](mailto:support@officespacesoftware.com).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do OfficeSpace Software no Painel de Acesso, você deverá entrar automaticamente no OfficeSpace Software para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


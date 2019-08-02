---
title: 'Tutorial: Integração do Azure Active Directory com o SumoLogic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 07874988c97044ede59c18b8f85a425483679431
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089543"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Tutorial: Integração do Azure Active Directory com o SumoLogic

Neste tutorial, você aprenderá a integrar o SumoLogic ao Azure AD (Azure Active Directory).
A integração do SumoLogic ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao SumoLogic.
* Você pode permitir que os usuários entrem automaticamente no SumoLogic (logon único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SumoLogic, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do SumoLogic habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SumoLogic é compatível com o SSO iniciado por **IdP**

## <a name="adding-sumologic-from-the-gallery"></a>Adicionando SumoLogic pela galeria

Para configurar a integração do SumoLogic ao Azure AD, você precisará adicionar o SumoLogic pela galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SumoLogic pela galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SumoLogic**, selecione **SumoLogic** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SumoLogic na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SumoLogic, usando um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SumoLogic.

Para configurar e testar o logon único do Azure AD com o SumoLogic, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do SumoLogic](#configure-sumologic-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SumoLogic](#create-sumologic-test-user)** – para que haja um equivalente de Brenda Fernandes no SumoLogic que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SumoLogic, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SumoLogic**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de domínio e de URLs do SumoLogic para logon único](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenantname>.SumoLogic.com`

   b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente do SumoLogic](https://www.sumologic.com/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o SumoLogic**, copie as URLs apropriadas, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-sumologic-single-sign-on"></a>Configurar o logon único do SumoLogic

1. Em outra janela do navegador da Web, entre no site da empresa do SumoLogic como administrador.

1. Vá para **Gerenciar \> Segurança**.

    ![Gerenciar](./media/sumologic-tutorial/ic778556.png "Gerenciar")

1. Clique em **SAML**.

    ![Configurações de segurança global](./media/sumologic-tutorial/ic778557.png "Configurações de segurança global")

1. Na lista **Selecionar uma configuração ou criar uma nova**, selecione **Azure AD** e clique em **Configurar**.

    ![Configurar o SAML 2.0](./media/sumologic-tutorial/ic778558.png "Configurar o SAML 2.0")

1. No diálogo **Configurar SAML 2.0** , realize as seguintes etapas:

    ![Configurar o SAML 2.0](./media/sumologic-tutorial/ic778559.png "Configurar o SAML 2.0")

    a. Na caixa de texto **Nome da Configuração**, digite **Azure AD**.

    b. Selecione **Modo de Depuração**.

    c. Na caixa de texto **Emissor**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.

    d. Na caixa de texto **URL de Solicitação do Authn**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .

    f. Para **Atributo de Email**, selecione **Usar entidade do SAML**.  

    g. Selecione **Configuração de Logon iniciada pelo SP**.

    h. Na caixa de texto **Caminho de Logon**, digite **Azure** e clique em **Salvar**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao SumoLogic.

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, selecione **SumoLogic**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SumoLogic**.

    ![O link do SumoLogic na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sumologic-test-user"></a>Criar um usuário de teste do SumoLogic

Para que os usuários do Azure AD possam entrar no SumoLogic, eles precisam ser provisionados no SumoLogic. No caso do SumoLogic, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. entre no seu locatário do **SumoLogic**.

1. Vá para **Gerenciar \> Usuários**.

    ![Usuários](./media/sumologic-tutorial/ic778561.png "Usuários")

1. Clique em **Adicionar**.

    ![Usuários](./media/sumologic-tutorial/ic778562.png "Usuários")

1. No diálogo **Novo Usuário** , realize as seguintes etapas:

    ![Novo Usuário](./media/sumologic-tutorial/ic778563.png "Novo Usuário") 

    a. Digite as informações relacionadas da conta do Azure AD que você deseja provisionar nas caixas de texto **Nome**, **Sobrenome** e **Email**.
  
    b. Selecione uma função.
  
    c. Para **Status**, selecione **Ativo**.
  
    d. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do SumoLogic ou as APIs fornecidas pelo SumoLogic para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SumoLogic no Painel de Acesso, você será conectado automaticamente ao InTime para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Tutorial: Integração do Azure Active Directory ao Coupa | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Coupa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7b4db749d1191179b3f4f1d9bb13d77925697f2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67104541"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Integração do Azure Active Directory ao Coupa

Neste tutorial, você aprende a integrar o Coupa ao Azure AD (Azure Active Directory).
A integração do Coupa ao Microsoft Azure AD oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure AD quem tem acesso ao Coupa.
* Você pode permitir que os usuários sejam conectados automaticamente ao Coupa (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD ao Coupa, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Coupa

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Coupa dá suporte ao SSO iniciado por **SP**

## <a name="adding-coupa-from-the-gallery"></a>Adição do Coupa a partir da galeria

Para configurar a integração do Coupa ao Microsoft Azure AD, você precisa adicionar o Coupa à sua lista de aplicativos SaaS gerenciados a partir da galeria.

**Para adicionar o Coupa por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Coupa**, selecione **Coupa** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Coupa na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure AD com o Coupa, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Coupa.

Para configurar e testar o logon único do Microsoft Azure AD com o Coupa, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Coupa](#configure-coupa-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Coupa](#create-coupa-test-user)** – para ter um equivalente de Brenda Fernandes no Coupa que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Coupa, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Coupa**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Coupa](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esse valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do Coupa](https://success.coupa.com/Support/Contact_Us?) para obter esses valores.

    b. Na caixa **Identificador**, digite uma URL:

    | Ambiente  | URL |
    |:-------------|----|
    | Área restrita | `sso-stg1.coupahost.com`|
    | Produção | `sso-prd1.coupahost.com`|
    | | |

    c. Na caixa de texto **URL de resposta**, insira uma URL:

    | Ambiente | URL |
    |------------- |----|
    | Área restrita | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Produção | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Coupa**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-coupa-single-sign-on"></a>Configurar o logon único do Coupa

1. Faça logon em seu site de empresa do Coupa como administrador.

2. Vá para **Configuração \> Controle de Segurança**.

    ![Controles de Segurança](./media/coupa-tutorial/ic791900.png "Controles de Segurança")

3. Na seção **Fazer Logon usando as credenciais do Coupa** , realize as seguintes etapas:

    ![Metadados SP do Coupa](./media/coupa-tutorial/ic791901.png "Metadados SP do Coupa")

    a. Selecione **Fazer logon usando o SAML**.

    b. Clique em **Procurar** para carregar os metadados baixados a partir do portal do Azure.

    c. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Coupa.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Coupa**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Coupa**.

    ![O link do Coupa na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-coupa-test-user"></a>Criar um usuário de teste do Coupa

Para permitir que os usuários do Azure AD façam logon no Coupa, eles devem ser provisionados no Coupa.  

* No caso do Coupa, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Coupa** como administrador.

2. No menu na parte superior, clique em **Configuração** e em **Usuários**.

    ![Usuários](./media/coupa-tutorial/ic791908.png "Usuários")

3. Clique em **Criar**.

    ![Criar Usuários](./media/coupa-tutorial/ic791909.png "Criar Usuários")

4. Na seção **Criação de Usuário** , realize as seguintes etapas:

    ![Detalhes do Usuário](./media/coupa-tutorial/ic791910.png "Detalhes do Usuário")

    a. Digite os atributos **Logon**, **Nome**, **Sobrenome**, **ID de Logon Único** e **Email** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

    b. Clique em **Criar**.

    >[!NOTE]
    >O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa.
    >

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Coupa ou as APIs fornecidas pelo Coupa para provisionar as contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Coupa no Painel de Acesso, você deverá ser conectado automaticamente ao Coupa, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


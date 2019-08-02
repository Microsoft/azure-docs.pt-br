---
title: 'Tutorial: Integração do Azure Active Directory ao Intacct | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 4a80d354e0aed1d12bc64c99242e818787e93344
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099951"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Tutorial: Integração do Active Directory do Azure ao Intacct

Neste tutorial, você aprenderá como integrar o Intacct ao Azure AD (Azure Active Directory).
A integração do Intacct ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Intacct.
* Você pode permitir que os usuários entrem automaticamente no Intacct (logon único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Intacct, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do Intacct habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Intacct é compatível com o SSO iniciado por **IDP**

## <a name="adding-intacct-from-the-gallery"></a>Como adicionar o Intacct da galeria

Para configurar a integração do Intacct ao Azure AD, você precisa adicionar o Intacct por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Intacct por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Intacct**, selecione **Intacct** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Intacct na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Intacct usando um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Intacct.

Para configurar e testar o logon único do Azure AD com o Intacct, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Intacct](#configure-intacct-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Intacct](#create-intacct-test-user)** – para que haja um equivalente de Brenda Fernandes no Intacct que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Intacct, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Intacct**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de domínio e de URLs do Intacct para logon único](common/idp-reply.png)

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Resposta real. Entre em contato com a [equipe de suporte ao cliente do Intacct](https://us.intacct.com/support) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Intacct**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-intacct-single-sign-on"></a>Configurar o logon único do Intacct

1. Em outra janela do navegador da Web, entre em seu site de empresa do Intacct como administrador.

1. Clique na guia **Empresa** e em **Informações da Empresa**.

    ![Empresa](./media/intacct-tutorial/ic790037.png "Empresa")

1. Clique na guia **Segurança** e em **Editar**.

    ![Segurança](./media/intacct-tutorial/ic790038.png "Segurança")

1. Na seção **SSO (logon único)** , realize as seguintes etapas:

    ![Logon único](./media/intacct-tutorial/ic790039.png "logon único")

    a. Selecione **Habilitar logon único**.

    b. Para **Tipo de provedor de identidade**, selecione **SAML 2.0**.

    c. Na caixa de texto **URL do Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado no portal do Azure.

    e. Abra seu certificado codificado em **Base 64** no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa **Certificado**.

    f. Clique em **Save** (Salvar).

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao Intacct.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, escolha **Intacct**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Intacct**.

    ![O link do Intacct na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-intacct-test-user"></a>Criar um usuário de teste do Intacct

Para configurar os usuários do Azure AD para que possam entrar no Intacct, eles devem ser provisionados no Intacct. Para o Intacct, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Entre em seu locatário do **Intacct**.

1. Clique na guia **Empresa** e em **Usuários**.

    ![Usuários](./media/intacct-tutorial/ic790041.png "Usuários")

1. Clique na guia **Adicionar**.

    ![Adicionar](./media/intacct-tutorial/ic790042.png "Adicionar")

1. Na seção **Informações do Usuário** , realize as seguintes etapas:

    ![Informações do Usuário](./media/intacct-tutorial/ic790043.png "informações do Usuário")

    a. Insira a **ID de Usuário**, o **Sobrenome**, o **Nome**, o **Endereço de email**, o **Título** e o **Telefone** de uma conta do Azure AD que você deseja provisionar na seção **Informações do Usuário**.

    b. Selecione os **privilégios de Administrador** de uma conta do Azure AD que você deseja provisionar.

    c. Clique em **Save** (Salvar). O titular da conta do Azure AD receberá um email e um link para confirmar sua conta antes de se tornar ativo.

> [!NOTE]
> Para provisionar as contas de usuário do Azure AD, é possível usar qualquer outra ferramenta de criação da conta de usuário do Intacct ou APIs fornecidas pelo Intacct.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Intacct no Painel de Acesso, você será conectado automaticamente ao aplicativo Intacct para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


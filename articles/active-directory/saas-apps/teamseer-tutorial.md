---
title: 'Tutorial: Integração do Azure Active Directory com o TeamSeer | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3e505e4823db8c9f42bbff216f30326081110dcf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088976"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutorial: integração do Azure Active Directory ao TeamSeer

Neste tutorial, você aprenderá a integrar o TeamSeer ao Azure AD (Azure Active Directory).
A integração do TeamSeer ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao TeamSeer.
* É possível permitir que seus usuários entrem automaticamente no TeamSeer (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TeamSeer, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do TeamSeer

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O TeamSeer é compatível com o SSO iniciado por **SP**

## <a name="adding-teamseer-from-the-gallery"></a>Adicionar o TeamSeer da galeria

Para configurar a integração do TeamSeer ao Azure AD, é necessário adicionar o TeamSeer da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TeamSeer da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TeamSeer**, selecione **TeamSeer** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![TeamSeer na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o TeamSeer baseado em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TeamSeer.

Para configurar e testar o logon único do Azure AD com o TeamSeer, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único do TeamSeer](#configure-teamseer-single-sign-on)** – para configurar o logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do TeamSeer](#create-teamseer-test-user)** – para ter um equivalente de Brenda Fernandes no TeamSeer que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TeamSeer, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TeamSeer**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do TeamSeer](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o TeamSeer**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-teamseer-single-sign-on"></a>Configurar logon único do TeamSeer

1. Em outra janela do navegador da Web, entre no site da empresa TeamSeer como administrador.

1. Vá para **Administrador de RH**.

    ![Administrador de RH](./media/teamseer-tutorial/ic789634.png "Administrador de RH")

1. Clique em **Instalação**.

    ![Configuração](./media/teamseer-tutorial/ic789635.png "Configuração")

1. Clique em **Configurar detalhes do provedor de SAML**.

    ![Configurações do SAML](./media/teamseer-tutorial/ic789636.png "Configurações do SAML")

1. Na seção de detalhes do provedor de SAML, execute as seguintes etapas:

    ![Configurações do SAML](./media/teamseer-tutorial/ic789637.png "Configurações do SAML")

    a. Na caixa de texto **URL**, cole o valor **URL de Logon**, que você copiou do portal do Azure.

    b. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado Público do IdP**.

1. Para concluir a configuração do provedor de SAML, execute as seguintes etapas:

    ![Configurações do SAML](./media/teamseer-tutorial/ic789638.png "Configurações do SAML")

    a. Nos **Endereços de Email de Teste**, digite o endereço de email do usuário de teste.
  
    b. Na caixa de texto **Emissor** , digite a URL do Emissor do provedor de serviços.
  
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
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TeamSeer.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **TeamSeer**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TeamSeer**.

    ![O link do TeamSeer na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-teamseer-test-user"></a>Criar usuário de teste do TeamSeer

Para permitir que os usuários do Azure AD entrem no TeamSeer, eles devem ser provisionados no ShiftPlanning. No caso do TeamSeer, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site da empresa **TeamSeer** como administrador.

1. Acesse **Usuários \> Administradores de RH** e, em seguida, clique em **Executar o Assistente de novo usuário**.

    ![Administrador de RH](./media/teamseer-tutorial/ic789640.png "Administrador de RH")

1. Na seção **Detalhes do Usuário** , realize as seguintes etapas:

    ![Detalhes do Usuário](./media/teamseer-tutorial/ic789641.png "Detalhes do Usuário")

    a. Digite o **Nome**, **Sobrenome** e **Nome de usuário (endereço de email)** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas.
  
    b. Clique em **Próximo**.

1. Siga as instruções na tela para adicionar um novo usuário e clique em **Concluir**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do TeamSeer ou APIs fornecidas pelo TeamSeer para provisionar as contas de usuário do AD do Azure.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do TeamSeer no Painel de Acesso, você deverá entrar automaticamente no TeamSeer para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

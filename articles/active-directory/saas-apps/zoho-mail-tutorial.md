---
title: 'Tutorial: Integração do Azure Active Directory ao Zoho | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69c2599a2ddd09cbaf869bf4d9e21a8032855cce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086177"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Tutorial: Integração do Azure Active Directory ao Zoho

Neste tutorial, você aprenderá a integrar o Zoho ao Azure AD (Azure Active Directory).
A integração do Zoho com o Azure AD fornece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Zoho.
* Você pode permitir que os usuários sejam conectados automaticamente ao Zoho (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zoho, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Zoho

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zoho dá suporte ao SSO iniciado por **SP**

## <a name="adding-zoho-from-the-gallery"></a>Adicionar Zoho da galeria

Para configurar a integração do Zoho com o Azure AD, você precisará adicionar o Zoho à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Zoho da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zoho**, selecione **Zoho** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Zoho na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zoho, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zoho.

Para configurar e testar o logon único do Azure AD com o Zoho, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Zoho](#configure-zoho-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Zoho](#create-zoho-test-user)** – para ter um equivalente de Brenda Fernandes no Zoho que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Zoho, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zoho**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Zoho](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<company name>.zohomail.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do Zoho](https://www.zoho.com/mail/contact.html) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Zoho**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-zoho-single-sign-on"></a>Configurar o Logon Único do Zoho

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zoho Mail como administrador.

2. Vá para o **Painel de Controle**.
   
    ![Painel de controle](./media/zoho-mail-tutorial/ic789607.png "Painel de controle")

3. Clique na guia **Autenticação SAML** .
   
    ![Autenticação SAML](./media/zoho-mail-tutorial/ic789608.png "Autenticação SAML")

4. Na seção **Detalhes da Autenticação SAML** , realize as seguintes etapas:
   
    ![Detalhes de Autenticação SAML](./media/zoho-mail-tutorial/ic789609.png "Detalhes de Autenticação SAML")
   
    a. Na caixa de texto **URL de Logon**, cole a **URL de Logon** copiada no portal do Azure.
   
    b. Na caixa de texto **URL de Logoff**, cole a **URL de Logoff** copiada no portal do Azure.
   
    c. Na caixa de texto **Alterar URL de Senha**, cole **Alterar URL da Senha** copiada do portal do Azure.
       
    d. Abra o certificado codificado em BASE-64 baixado no portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **PublicKey**.
   
    e. Para **Algoritmo**, selecione **RSA**.
   
    f. Clique em **OK**.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Zoho.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Zoho**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Zoho**.

    ![O link do Zoho na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-zoho-test-user"></a>Criar um usuário de teste do Zoho

Para permitir que os usuários do AD do Azure façam logon no Zoho Mail, eles devem ser provisionados no Zoho Mail. No caso do Zoho Mail, o provisionamento é uma tarefa manual.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zoho Mail ou APIs fornecidas pelo Zoho Mail para provisionar as contas de usuário do AAD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa do **Zoho Mail** como administrador.

1. Vá para **Painel de Controle \> Emails e Documentos**.

1. Vá para **Detalhes do Usuário \> Adicionar Usuário**.
   
    ![Adicionar Usuário](./media/zoho-mail-tutorial/ic789611.png "Adicionar Usuário")

1. No diálogo **Adicionar usuários** , realize as seguintes etapas:
   
    ![Adicionar Usuário](./media/zoho-mail-tutorial/ic789612.png "Adicionar Usuário")
   
    a. Na caixa de texto **Nome**, digite o Nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o Sobrenome do usuário, como **Fernandes**.

    c. Na caixa de texto **ID do Email**, digite a ID do email do usuário como **brendafernandes\@contoso.com**.

    d. Na caixa de texto **Senha** digite a senha do usuário.
   
    e. Clique em **OK**.  
      
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Zoho no Painel de Acesso, você deverá ser conectado automaticamente ao Zoho, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


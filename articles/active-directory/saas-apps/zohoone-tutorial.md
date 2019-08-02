---
title: 'Tutorial: Integração do Azure Active Directory ao Zoho One | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086243"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Integração do Azure Active Directory ao Zoho One

Neste tutorial, você aprenderá como integrar o Zoho One ao Azure AD (Azure Active Directory).
A integração do Zoho One ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Zoho One.
* É possível permitir que seus usuários entrem automaticamente no Zoho One (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Zoho One, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura do Zoho One habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Zoho One é compatível com SSO iniciado por **SP** e **IDP**

## <a name="adding-zoho-one-from-the-gallery"></a>Adicionando Zoho One da galeria

Para configurar a integração do Zoho One ao Azure AD, você precisa adicionar o Zoho One da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar Zoho One da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zoho One**, selecione **Zoho One** no painel de resultados e clique no botão **Add** para adicionar o aplicativo.

     ![Zoho One na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zoho One com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zoho One.

Para configurar e testar o logon único do Azure AD com o Zoho One, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único do Zoho One](#configure-zoho-one-single-sign-on)** – para configurar as definições de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Zoho One](#create-zoho-one-test-user)** – para ter um equivalente de Brenda Fernandes no Zoho One que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Zoho One, siga as etapas abaixo:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zoho One**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    ![Zoho One Domain e informações de logon único de URLs](common/idp-relay.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `one.zoho.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > O valor **URL de Resposta** anterior não é real. Você obterá o valor `<saml-identifier>` da etapa 4 da seção **Configurar logon único do Zoho One**, explicada posteriormente no tutorial.

    c. Clique em **Definir URLs adicionais**.

    d. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `https://one.zoho.com`

5. Se quiser configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:


    ![Zoho One Domain e informações de logon único de URLs](common/both-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > O valor **URL de Entrada** anterior não é real. Você atualizará o valor com a URL de Entrada real da seção **Configurar Logon Único do Zoho One**, explicado posteriormente no tutorial. 

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

7. Na seção **Configurar o Zoho One**, copie a URL apropriada de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-zoho-one-single-sign-on"></a>Configurar logon único do Zoho One

1. Em uma janela diferente do navegador da Web, entre no site da sua empresa Zoho One como administrador.

2. Na guia **Organização**, clique em **Configuração** em **Autenticação SAML**.

    ![Org. Zoho One](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Na página Pop-up, execute as seguintes etapas:

    ![Assinatura Zoho One](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Na caixa de texto **URL de Conexão**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Na caixa de texto **URL de Saída**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    c. Clique em **Procurar** para carregar o **Certificado (Base64)** que você baixou do portal do Azure.

    d. Clique em **Save** (Salvar).

4. Após salvar a configuração de Autenticação SAML, copie o valor **SAML-Identifier** e acrescente-o à **URL de Resposta** no lugar de `<saml-identifier>`, como `https://accounts.zoho.com/samlresponse/one.zoho.com`, e cole o valor gerado na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML**.

    ![SAML do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Acesse a guia **Domínios** e, em seguida, clique em **Adicionar Domínio**.

    ![Domínio do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Na página**Adicionar Domínio**, execute as seguintes etapas:

    ![Adicionar domínio do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Na caixa de texto **Nome de Domínio**, digite um domínio como contoso.com.

    b. Clique em **Adicionar**.

    >[!Note]
    >Depois de adicionar o domínio, siga [estas](https://www.zoho.com/one/help/admin-guide/domain-verification.html) etapas para verificar o domínio. Depois que o domínio for verificado, use o nome do domínio na **URL de Entrada** na seção **Configuração Básica de SAML** no portal do Azure.

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Zoho One.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Zoho One**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Zoho One**.

    ![O link do Zoho One na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-zoho-one-test-user"></a>Criar usuário de teste do Zoho One

Para permitir que os usuários do Azure AD entrem no Zoho One, eles devem ser provisionados no Zoho One. No Zoho One, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Zoho One como Administrador de Segurança.

2. Na guia **Usuários**, clique em **logotipo do usuário**.

    ![Usuário do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Na página **Adicionar Usuário**, realize as seguintes etapas:

    ![Adicionar usuário do Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Na caixa de texto **Nome**, insira o nome do usuário como **Brenda Fernandes**.
    
    b. Na caixa de texto **Endereço de Email**, insira o email do usuário como brittasimon@contoso.com.

    >[!Note]
    >Selecione o domínio verificado na lista de domínios.

    c. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Zoho One no Painel de Acesso, deverá ser conectado automaticamente ao Zoho One para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


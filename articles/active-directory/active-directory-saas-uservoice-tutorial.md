---
title: "Tutorial: Integração do Azure Active Directory com o UserVoice | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o UserVoice."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: fcfda1c2ecb162fb93b70574a18bd745b72ee4db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integração do Azure Active Directory com o UserVoice

Neste tutorial, você aprenderá a integrar o UserVoice ao Azure AD (Azure Active Directory).

A integração do UserVoice ao Azure AD oferece os seguintes benefícios:

- No AD do Azure, é possível controlar quem tem acesso ao UserVoice.
- Você pode permitir que seus usuários façam logon automaticamente no UserVoice (logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao UserVoice, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do UserVoice com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar UserVoice pela galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-uservoice-from-the-gallery"></a>Adicionar UserVoice pela galeria
Para configurar a integração do UserVoice com o Azure AD, você precisará adicionar o UserVoice à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o UserVoice pela galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **UserVoice**, selecione **UserVoice** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![UserVoice na lista de resultados](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o UserVoice, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do UserVoice é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no UserVoice.

No UserVoice, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o UserVoice, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do UserVoice](#create-a-uservoice-test-user)** – para ter um equivalente de Brenda Fernandes no UserVoice que esteja vinculado à representação de usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo UserVoice.

**Para configurar o logon único do Azure AD com o UserVoice, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **UserVoice**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. Na seção **URLs e Domínio do UserVoice**, execute as seguintes etapas:

    ![Informações de logon único de URLs e Domínio do UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenantname>.UserVoice.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte do cliente do UserVoice](https://www.uservoice.com/) para obter esses valores.

4. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL** do certificado.

    ![O link de download do Certificado](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-uservoice-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do UserVoice**, clique em **Configurar UserVoice** para abrir a janela **Configurar logon**. Copie a **URL de Saída e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configuração do UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_configure.png) 

7. Em uma janela diferente do navegador da Web, faça logon no site corporativo do UserVoice como administrador.

8. Na barra de ferramentas na parte superior, clique em **Configurações** e, em seguida, selecione o **portal da Web** no menu.
   
    ![Seção Configurações no lado do aplicativo](./media/active-directory-saas-uservoice-tutorial/ic777519.png "Configurações")

9. Na guia **Portal da Web**, na seção **Autenticação de usuário**, clique em **Editar** para abrir a página do diálogo **Editar Autenticação de Usuário**.
   
    ![Guia Portal da Web](./media/active-directory-saas-uservoice-tutorial/ic777520.png "Portal da Web")

10. Na página do diálogo **Editar Autenticação de Usuário** , realize as seguintes etapas:
   
    ![Editar autenticação de usuário](./media/active-directory-saas-uservoice-tutorial/ic777521.png "Editar autenticação de usuário")
   
    a. Clique em **SSO (Logon Único)**.
 
    b. Cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure na caixa de texto **Entrada remota de SSO**.

    c. Cole o valor da **URL de Saída** copiado do portal do Azure na caixa de texto **Entrada remota de SSO**.
 
    d. Cole o valor da **Impressão digital** copiado do portal do Azure na caixa de texto **Impressão digital de certificado SHA1 atual**.
    
    e. Clique em **Salvar Configurações de Autenticação**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-uservoice-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-uservoice-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-uservoice-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-uservoice-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-uservoice-test-user"></a>Criar um usuário de teste do UserVoice

Para permitir que os usuários do Azure AD façam logon no UserVoice, eles devem ser provisionados no UserVoice. No caso do UserVoice, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:
1. Faça logon no seu locatário **UserVoice** .

2. Vá para **Configurações**.
   
    ![Configurações](./media/active-directory-saas-uservoice-tutorial/ic777811.png "Configurações")

3. Clique em **Geral**.

4. Clique em **Agentes e permissões**.
   
    ![Agentes e permissões](./media/active-directory-saas-uservoice-tutorial/ic777812.png "Agentes e permissões")

5. Clique em **Adicionar administradores**.
   
    ![Adicionar administradores](./media/active-directory-saas-uservoice-tutorial/ic777813.png "Adicionar administradores")

6. No diálogo **Convidar administradores** , realize as seguintes etapas:
   
    ![Convidar administradores](./media/active-directory-saas-uservoice-tutorial/ic777814.png "Convidar administradores")
   
    a. Na caixa de texto Emails, digite o endereço de email da conta que você deseja provisionar e clique em **Adicionar**.
   
    b. Clique em **Convidar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário do UserVoice ou APIs fornecidas pelo UserVoice para provisionar contas de usuário do AAD.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao UserVoice.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao UserVoice, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **UserVoice**.

    ![O link do UserVoice na lista de Aplicativos](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco UserVoice no Painel de Acesso, deverá ser conectado automaticamente ao seu aplicativo UserVoice.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_203.png


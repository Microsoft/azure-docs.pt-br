---
title: "Tutorial: integração do Azure Active Directory com o Citrix ShareFile | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Citrix ShareFile."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: b85680104fe4f33638c559b2a12483a2312a4476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: integração do Azure Active Directory ao Citrix ShareFile

Nesse tutorial, você aprenderá a integrar o Citrix ShareFile ao Azure AD (Azure Active Directory).

A integração do Citrix ShareFile ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Citrix ShareFile.
- Você pode permitir que seus usuários façam logon automaticamente no Citrix ShareFile usando logon único com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Citrix ShareFile, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Citrix ShareFile

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Citrix ShareFile por meio da galeria
2. Configurar e testar logon único do Azure AD

## <a name="add-citrix-sharefile-from-the-gallery"></a>Adicionar o Citrix ShareFile por meio da galeria
Para configurar a integração do Citrix ShareFile com o Azure AD, você precisa adicionar o Citrix ShareFile, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Citrix ShareFile da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Citrix ShareFile**, selecione **Citrix ShareFile** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Citrix ShareFile na lista de resultados](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Citrix ShareFile, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Citrix ShareFile é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Citrix ShareFile.

No Citrix ShareFile, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Citrix ShareFile, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Citrix ShareFile](#create-a-citrix-sharefile-test-user)** – para ter um equivalente de Brenda Fernandes no Citrix ShareFile que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único em seu aplicativo Citrix ShareFile.

**Para configurar o logon único do Azure AD com o Citrix ShareFile, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Citrix ShareFile**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_samlbase.png)

3. Na seção **URLs e Domínio do Citrix ShareFile**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_url.png)
    
    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.sharefile.com/saml/login`

    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do Citrix ShareFile](https://www.citrix.co.in/products/sharefile/support.html) para obter esse valor. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-sharefile-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Citrix ShareFile**, clique em **Configurar Citrix ShareFile** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_configure.png) 

7. Em outra janela do navegador da Web, faça logon em seu site de empresa do **Citrix ShareFile** como administrador.

8. Na barra de ferramentas na parte superior, clique em **Administrador**.

9. No painel de navegação à esquerda, selecione **Configurar Logon Único**.
   
    ![Administração de Conta](./media/active-directory-saas-sharefile-tutorial/ic773627.png "Administração de Conta")

10. Na página **Configuração de Logon Único/SAML 2.0** em **Configurações Básicas**, realize as seguintes etapas:
   
    ![Logon Único](./media/active-directory-saas-sharefile-tutorial/ic773628.png "Logon Único")
   
    a. Clique em **Habilitar SAML**.
    
    b. Na caixa de texto **ID da Entidade/Emissor do IDP**, cole o valor da **ID da Entidade SAML** copiado do Portal do Azure.

    c. Clique em **Alterar** ao lado do campo **Certificado X.509** e carregue o certificado baixado no portal Azure.
    
    d. Na caixa de texto **URL de Logon**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure.
    
    e. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Saída** copiado do portal do Azure.

11. Clique em **Salvar** no portal de gerenciamento do Citrix ShareFile.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Criar um usuário de teste do Citrix ShareFile

Para permitir que os usuários do Azure AD façam logon no Citrix ShareFile, eles devem ser provisionados no Citrix ShareFile. No caso do Citrix ShareFile, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Citrix ShareFile** .

2. Clique em **Gerenciar Usuários \> Gerenciar Página Inicial dos Usuários \> + Criar Funcionário**.
   
   ![Criar Funcionário](./media/active-directory-saas-sharefile-tutorial/IC781050.png "Criar Funcionário")

3. Na seção **Informações Básicas**, execute as etapas abaixo:
   
   ![Informações Básicas](./media/active-directory-saas-sharefile-tutorial/IC799951.png "Informações Básicas")
   
   a. Na caixa de texto **Endereço de Email**, digite o endereço de email de Brenda Fernandes como **brittasimon@contoso.com**.
   
   b. Na caixa de texto **Nome**, digite o **nome** do usuário como **Brenda**.
   
   c. Na caixa de texto **Sobrenome**, digite o **sobrenome** do usuário como **Fernandes**.

4. Clique em **Adicionar Usuário**.
  
   >[!NOTE]
   >O titular da conta do Azure AD receberá um email e seguirá um link para confirmar sua conta antes de se tornar ativo. Você pode usar quaisquer outras ferramentas de criação de conta de usuário do Citrix ShareFile ou APIs fornecidas pelo Citrix ShareFile para provisionar contas de usuário do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Citrix ShareFile.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Citrix ShareFile, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Citrix ShareFile**.

    ![O link do Citrix ShareFile na lista de Aplicativos](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Citrix ShareFile. no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Citrix ShareFile.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png


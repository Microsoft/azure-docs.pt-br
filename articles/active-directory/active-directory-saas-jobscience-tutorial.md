---
title: "Tutorial: integração do Azure Active Directory com o Jobscience | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Jobscience."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 66bec35a8f17482433dbf02827b90620d1cff378
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Integração do Active Directory do Azure com o Jobscience

Neste tutorial, você aprende a integrar o Jobscience ao Azure AD (Azure Active Directory).

A integração do Jobscience ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Jobscience
- É possível permitir que os usuários se conectem automaticamente ao Jobscience (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Jobscience, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Jobscience

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Jobscience por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-jobscience-from-the-gallery"></a>Adicionando o Jobscience por meio da galeria
Para configurar a integração do Jobscience ao Azure AD, é necessário adicionar o Jobscience à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Jobscience por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Jobscience**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_search.png)

5. No painel de resultados, selecione **Jobscience** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Jobscience, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Jobscience é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Jobscience.

No Jobscience, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Jobscience, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Jobscience](#creating-a-jobscience-test-user)** – para ter um equivalente de Brenda Fernandes no Jobscience que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Jobscience.

**Para configurar o logon único do Azure AD com o Jobscience, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Jobscience**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_samlbase.png)

3. Na seção **Domínio e URLs do Jobscience**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Obtenha esse valor com a [equipe de suporte ao Cliente do Jobscience](https://www.jobscience.com/support) ou no perfil de SSO que você criará, explicado adiante no tutorial. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-jobscience-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Jobscience**, clique em **Configurar o Jobscience** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_configure.png) 

7. Faça logon no site da sua empresa Jobscience como um administrador.

8. Vá para **Configuração**.
   
   ![Configuração](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Configuração")

9. No painel de navegação à esquerda, na seção **Administrador**, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e clique em **Meu Domínio** para abrir a página **Meu Domínio**. 
   
   ![Meu Domínio](./media/active-directory-saas-jobscience-tutorial/ic767825.png "Meu Domínio")

10. Para confirmar se o domínio foi configurado corretamente, verifique se ele está em “**Etapa 4 Implantado nos Usuários**” e examine “**Minhas Configurações de Domínio**”.

    ![Domínio Implantado no Usuário](./media/active-directory-saas-jobscience-tutorial/ic784377.png "Domínio Implantado no Usuário")

11. No site de empresa do Jobscience, clique em **Controles de Segurança** e clique em **Configurações de Logon Único**.
    
    ![Controles de Segurança](./media/active-directory-saas-jobscience-tutorial/ic784364.png "Controles de Segurança")

12. Na seção **Configurações de Logon Único** , realize as seguintes etapas:
    
    ![Configurações de Logon Único](./media/active-directory-saas-jobscience-tutorial/ic781026.png "Configurações de Logon Único")
    
    a. Selecione **SAML Habilitado**.

    b. Clique em **Novo**.

13. No diálogo **Editar Configuração de Logon Único do SAML** , realize as seguintes etapas:
    
    ![Configurações de Logon Único do SAML](./media/active-directory-saas-jobscience-tutorial/ic784365.png "Configurações de Logon Único do SAML")
    
    a. Na caixa de texto **Nome** , digite um nome para a sua configuração.

    b. Na caixa de texto **Emissor**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    c. Na caixa de texto **ID da Entidade**, digite `https://salesforce-jobscience.com`

    d. Clique em **Procurar** para carregar o certificado do Azure AD.

    e. Para o **Tipo de Identidade SAML**, selecione **A declaração contém a ID de Federação do objeto de Usuário**.

    f. Para **Local de Identidade do SAML**, selecione **A identidade está contida no elemento NameIdentifier da instrução Subject**.

    g. Na caixa de texto **URL de Logon do Provedor de Identidade**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    h. Na caixa de texto **URL de Logoff do Provedor de Identidade**, cole o valor da **URL de Saída** copiado do portal do Azure.

    i. Clique em **Salvar**.

14. No painel de navegação à esquerda, na seção **Administrador**, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e clique em **Meu Domínio** para abrir a página **Meu Domínio**. 
    
    ![Meu Domínio](./media/active-directory-saas-jobscience-tutorial/ic767825.png "Meu Domínio")

15. Na página **Meu Domínio**, na seção **Identidade Visual da Página de Logon**, clique em **Editar**.
    
    ![Identidade Visual da Página de Logon](./media/active-directory-saas-jobscience-tutorial/ic767826.png "Identidade Visual da Página de Logon")

16. Na página **Identidade Visual da Página de Logon**, na seção **Serviço de Autenticação**, o nome das **Configurações de SSO do SAML** é exibido. Selecione-o e, em seguida, clique em **Salvar**.
    
    ![Identidade Visual da Página de Logon](./media/active-directory-saas-jobscience-tutorial/ic784366.png "Identidade Visual da Página de Logon")

17. Para obter a URL de Logon Único iniciada pelo SP, clique em **Configurações de Logon Único** na seção do menu **Controles de Segurança**.

    ![Controles de Segurança](./media/active-directory-saas-jobscience-tutorial/ic784368.png "Controles de Segurança")
    
    Clique no perfil SSO que você criou na etapa anterior. Essa página mostra a URL de Logon Único de sua empresa (por exemplo, [https://companyname.my.salesforce.com?so=companyid](https://companyname.my.salesforce.com?so=companyid)).    

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jobscience-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jobscience-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jobscience-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-jobscience-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-jobscience-test-user"></a>Criando um usuário de teste do Jobscience

Para permitir que os usuários do Azure AD façam logon no Jobscience, eles devem ser provisionados no Jobscience. No caso do Jobscience, o provisionamento é uma tarefa manual.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do Jobscience ou as APIs fornecidas pelo Jobscience para provisionar contas de usuário do Azure Active Directory.
>  
        
**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Jobscience** como administrador.

2. Vá para a Configuração.
   
   ![Configuração](./media/active-directory-saas-jobscience-tutorial/ic784358.png "Configuração")
3. Vá para **Gerenciar Usuários \> Usuários**.
   
   ![Usuários](./media/active-directory-saas-jobscience-tutorial/ic784369.png "Usuários")
4. Clique em **Novo Usuário**.
   
   ![Todos os Usuários](./media/active-directory-saas-jobscience-tutorial/ic784370.png "Todos os Usuários")
5. No diálogo **Editar Usuário** , realize as seguintes etapas:
   
   ![Editar Usuário](./media/active-directory-saas-jobscience-tutorial/ic784371.png "Editar Usuário")
   
   a. Na caixa de texto **Nome**, digite um nome do usuário, como Brenda.
   
   b. Na caixa de texto **Sobrenome**, digite um sobrenome do usuário, como Fernandes.
   
   c. Na caixa de texto **Alias**, digite um nome de alias do usuário, como brendaf.

   d. Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

   e. Na caixa de texto **Nome de Usuário**, digite um nome de usuário do usuário, como Brittasimon@contoso.com.

   f. Na caixa de texto **Apelido**, digite um apelido do usuário, como Fernandes.

   g. Clique em **Salvar**.

    
> [!NOTE]
> O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Jobscience.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Jobscience, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Jobscience**.

    ![Configurar Logon Único](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Jobscience no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo Jobscience.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_203.png


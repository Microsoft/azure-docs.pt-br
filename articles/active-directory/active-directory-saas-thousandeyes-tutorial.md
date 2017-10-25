---
title: "Tutorial: Integração do Azure Active Directory ao ThousandEyes | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o ThousandEyes."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: jeedes
ms.openlocfilehash: 392add7d5f0a55598b8b90760f5c3f2d1e67ac02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutorial: Integração do Active Directory do Azure ao ThousandEyes

Neste tutorial, você aprenderá a integrar o ThousandEyes ao Azure AD (Azure Active Directory).

A integração do ThousandEyes com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao ThousandEyes
- É possível permitir que os usuários entrem automaticamente no ThousandEyes (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ThousandEyes, serão necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do ThousandEyes habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o ThousandEyes da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-thousandeyes-from-the-gallery"></a>Adicionar o ThousandEyes da galeria
Para configurar a integração do ThousandEyes com o Azure AD, é necessário adicionar o ThousandEyes da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ThousandEyes da galeria, siga as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **ThousandEyes**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_search.png)

5. No painel de resultados, selecione **ThousandEyes** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o ThousandEyes, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ThousandEyes é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ThousandEyes.

No ThousandEyes, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o ThousandEyes, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do ThousandEyes](#creating-a-thousandeyes-test-user)** – para ter um equivalente de Brenda Fernandes no ThousandEyes que esteja vinculado à representação de usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo ThousandEyes.

**Para configurar o logon único do Azure AD com o ThousandEyes, siga estas etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **ThousandEyes**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

3. Na seção **URLs e Domínio do ThousandEyes**, siga estas etapas:

    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL como: `https://app.thousandeyes.com/login/sso`

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do ThousandEyes**, clique em **Configurar ThousandEyes** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

7. Em outra janela do navegador da Web, entre em seu site de empresa do **ThousandEyes** como administrador.

8. No menu na parte superior, clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-thousandeyes-tutorial/ic790066.png "Configurações")

9. Clique em **Conta**
   
    ![Conta](./media/active-directory-saas-thousandeyes-tutorial/ic790067.png "Conta")

10. Clique na guia **Segurança e Autenticação**.
   
    ![Segurança e autenticação](./media/active-directory-saas-thousandeyes-tutorial/ic790068.png "Segurança e autenticação")

11. Na seção **Configurações de Logon Único** , realize as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-thousandeyes-tutorial/ic790069.png "Configurar o logon único")
  
    a. Selecione **Habilitar Logon Único**.
  
    b. Na caixa de texto **URL de Página de Logon**, cole a **URL do Serviço de Logon Único SAML** copiada do Portal do Azure.
  
    c. Na caixa de texto **URL de Página de Logoff**, cole a **URL de Saída** copiada do Portal do Azure.
  
    d. Na caixa de texto **Emissor do Provedor de Identidade**, cole a **ID de Entidade SAML** copiada do Portal do Azure.
  
    e. Em **Certificado de Verificação**, clique em **Escolher Arquivo** e carregue o certificado que você baixou do Portal do Azure.
  
    f. Clique em **Salvar**.
 
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-thousandeyes-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-thousandeyes-test-user"></a>Criação de um usuário de teste do ThousandEyes

Para permitir que os usuários do AD do Azure façam logon no ThousandEyes, eles devem ser provisionados no ThousandEyes.  
No caso do ThousandEyes, o provisionamento é uma tarefa manual.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do ThousandEyes ou as APIs fornecidas pelo ThousandEyes para provisionar as contas de usuário do Azure Active Directory.

**Para provisionar uma conta de usuário no ThousandEyes, execute as seguintes etapas:**

1. Faça logon em seu site de empresa ThousandEyes como um administrador.

2. Clique em **Configurações**.
   
    ![Configurações](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Configurações")

3. Clique em **Conta**.
   
    ![Conta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Conta")

4. Clique na guia **Contas e Usuários**.
   
    ![Contas e usuários](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Contas e usuários")

5. Na seção **Adicionar Usuários e Contas**, realize as seguintes etapas:
   
    ![Adicionar contas de usuário](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Adicionar contas de usuário")   
  
    a. Na caixa de texto **Nome**, digite o nome do usuário como **Brenda Fernandes**.

    b. Na caixa de texto **Email**, digite o email do usuário como **brittasimon@contoso.com**.
   
    b. Clique em **Adicionar Novo Usuário à Conta**.
      
     >[!NOTE]
     >O titular da conta do Azure Active Directory receberá um email com um link para confirmar e ativar a conta.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao ThousandEyes.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao ThousandEyes, siga estas etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **ThousandEyes**.

    ![Configurar Logon Único](./media/active-directory-saas-thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco ThousandEyes no Painel de Acesso, você deverá entrará automaticamente no seu aplicativo ThousandEyes.

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thousandeyes-tutorial/tutorial_general_203.png


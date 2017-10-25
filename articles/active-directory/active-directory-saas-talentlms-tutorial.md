---
title: "Tutorial: integração do Azure Active Directory com o TalentLMS | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o TalentLMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: f28d6fbfad9dae578a20db7218b7e3b174ed859c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: integração do Azure Active Directory com o TalentLMS

Neste tutorial, você aprenderá a integrar o TalentLMS ao Azure AD (Azure Active Directory).

A integração do TalentLMS com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao TalentLMS
- É possível permitir que seus usuários entrem automaticamente no TalentLMS (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o TalentLMS, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do TalentLMS

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o TalentLMS da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-talentlms-from-the-gallery"></a>Adicionar o TalentLMS da galeria
Para configurar a integração do TalentLMS com o Azure AD, é necessário adicionar o TalentLMS da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TalentLMS da galeria, siga as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **TalentLMS**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_search.png)

5. No painel de resultados, selecione **TalentLMS** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o TalentLMS, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do TalentLMS é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TalentLMS.

No TalentLMS, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o TalentLMS, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do TalentLMS](#creating-a-talentlms-test-user)** – para ter um equivalente de Brenda Fernandes no TalentLMS que esteja vinculado à representação de usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único em seu aplicativo TalentLMS.

**Para configurar o logon único do Azure AD com o TalentLMS, siga as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **TalentLMS**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_samlbase.png)

3. Na seção **URLs e Domínio do TalentLMS**, siga as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.TalentLMSapp.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `http://<tenant-name>.talentlms.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do TalentLMS](https://www.talentlms.com/contact) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL** do certificado.

    ![Configurar Logon Único](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-talentlms-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do TalentLMS**, clique em **Configurar o TalentLMS** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_configure.png)  

7. Em outra janela do navegador da Web, faça logon em seu site da empresa TalentLMS como administrador.

8. Na seção **Conta e Configurações**, clique na guia **Usuários**.
   
    ![Conta e Configurações](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Conta e Configurações")

9. Clique em **SSO (Logon Único)**.

10. Na seção de Configurações de Logon Único, execute as seguintes etapas:
   
    ![Logon Único](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Logon Único")   

    a. Na lista **Tipo de integração de SSO**, selecione **SAML 2.0**.

    b. Na caixa de texto **IDP (Provedor de Identidade)**, cole o valor da **ID da entidade SAML** que você copiou do Portal do Azure.
 
    c. Cole o valor da **Impressão digital** do Portal do Azure na caixa de texto **Impressão digital do certificado**.    

    d.  Na caixa de texto **URL de Entrada Remota**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do Portal do Azure.
 
    e. Na caixa de texto **URL de Saída Remota**, cole o valor da **URL de Saída** copiado do Portal do Azure.

    f. Preencha o seguinte: 

    * Na caixa de texto **TargetedID**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
     
    * Na caixa de texto **Nome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    
    * Na caixa de texto **Sobrenome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
    
    * Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    
11. Clique em **Salvar**.
 
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-talentlms-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-talentlms-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-talentlms-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-talentlms-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-talentlms-test-user"></a>Criação de um usuário de teste do TalentLMS

Para permitir que os usuários do Azure AD façam logon no TalentLMS, eles devem ser provisionados no TalentLMS. No caso do TalentLMS, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **TalentLMS** .

2. Clique em **Usuários** e em **Adicionar Usuário**.

3. Na página do diálogo **Adicionar usuário** , realize as seguintes etapas:
   
    ![Adicionar Usuário](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Adicionar Usuário")  

    a. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.
 
    c. Na caixa de texto **Endereço de email**, insira o email do usuário como **brittasimon@contoso.com**.

    d. Clique em **Adicionar Usuário**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do TalentLMS ou as APIs fornecidas pelo TalentLMS para provisionar as contas de usuário do AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TalentLMS.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao TalentLMS, siga as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **TalentLMS**.

    ![Configurar Logon Único](./media/active-directory-saas-talentlms-tutorial/tutorial_talentlms_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco TalentLMS no Painel de Acesso, você deverá entrar automaticamente no seu aplicativo TalentLMS

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-talentlms-tutorial/tutorial_general_203.png


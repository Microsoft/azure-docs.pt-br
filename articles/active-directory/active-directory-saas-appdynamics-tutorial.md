---
title: "Tutorial: Integração do Azure Active Directory ao AppDynamics | Microsoft Azure"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o AppDynamics."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 634e68bdb937eba68b27b824dc62fe2677e24ffe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Tutorial: Integração do Active Directory do Azure ao AppDynamics

Neste tutorial, você aprende a integrar o AppDynamics ao Azure AD (Azure Active Directory).

A integração do AppDynamics ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao AppDynamics
- É possível permitir que os usuários se conectem automaticamente ao AppDynamics (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao AppDynamics, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do AppDynamics

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o AppDynamics por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-appdynamics-from-the-gallery"></a>Adicionando o AppDynamics por meio da galeria
Para configurar a integração do AppDynamics ao Azure AD, você precisa adicionar o AppDynamics à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o AppDynamics por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **AppDynamics**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_search.png)

5. No painel de resultados, selecione **AppDynamics** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o AppDynamics, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do AppDynamics é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do AppDynamics.

No AppDynamics, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o AppDynamics, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do AppDynamics](#creating-an-appdynamics-test-user)** – para ter um equivalente de Brenda Fernandes no AppDynamics que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo AppDynamics.

**Para configurar o logon único do Azure AD com o AppDynamics, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **AppDynamics**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_samlbase.png)

3. Na seção **Domínio e URLs do AppDynamics**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.saas.appdynamics.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do AppDynamics](https://www.appdynamics.com/support/) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-appdynamics-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do AppDynamics**, clique em **Configurar o AppDynamics** para abrir a janela **Configurar logon**. Copie a **URL de Saída e a URL do Serviço de Logon Único SAML** da **seção Referência rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_configure.png) 

7. Em outra janela do navegador da Web, faça logon em seu site de empresa do AppDynamics como administrador.

8. Na barra de ferramentas na parte superior, clique em **Configurações** e em **Administração**.
   
    ![Administração](./media/active-directory-saas-appdynamics-tutorial/ic790216.png "Administração")

9. Clique na guia **Provedor de Autenticação** .
   
    ![Provedor de Autenticação](./media/active-directory-saas-appdynamics-tutorial/ic790224.png "Provedor de Autenticação")

10. Na seção **Provedor de Autenticação** , realize as seguintes etapas:
   
    ![Configuração SAML](./media/active-directory-saas-appdynamics-tutorial/ic790225.png "configuração SAML")   

    a. Como **Provedor de Autenticação**, selecione **SAML**.

    b. Na caixa de texto **URL de Logon**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Saída** copiado do portal do Azure.
       
    d. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado**

    e. Clique em **Salvar**.

     ![Salvar](./media/active-directory-saas-appdynamics-tutorial/ic777673.png "Salvar")

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-appdynamics-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-appdynamics-test-user"></a>Criando um usuário de teste do AppDynamics

Para permitir que os usuários do Azure AD façam logon no AppDynamics, eles devem ser provisionados no AppDynamics. No caso do AppDynamics, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do AppDynamics como administrador.

2. Vá para **Usuários**, e, em seguida, clique em **+** para abrir o diálogo **Criar Usuário**.
   
    ![Usuários](./media/active-directory-saas-appdynamics-tutorial/ic790229.png "Usuários")

3. Na seção **Criar Usuário** , realize as seguintes etapas:
   
    ![Criar usuário](./media/active-directory-saas-appdynamics-tutorial/ic790230.png "Criar usuário")
   
    a. Preencha os campos **Nome de usuário**, **Nome**, **Email**, **Nova Senha**, **Repetir Nova Senha** de uma conta válida do AAD que deseja provisionar nas caixas de texto relacionadas.

    b. Clique em **Salvar**.

    >[!NOTE]
    >Você pode usar qualquer outra ferramenta de criação da conta de usuário do AppDynamics ou as APIs fornecidas pelo AppDynamics para provisionar as contas de usuário do AD do Azure.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao AppDynamics.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao AppDynamics, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **AppDynamics**.

    ![Configurar Logon Único](./media/active-directory-saas-appdynamics-tutorial/tutorial_appdynamics_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco AppDynamics no Painel de Acesso, deverá ser automaticamente conectado ao aplicativo AppDynamics.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-appdynamics-tutorial/tutorial_general_203.png


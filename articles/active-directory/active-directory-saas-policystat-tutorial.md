---
title: "Tutorial: Integração do Azure Active Directory ao PolicyStat | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o PolicyStat."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 704afd5515b02ce2a4fbf35da65fad74dc506271
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integração do Azure Active Directory ao PolicyStat

Neste tutorial, você aprende a integrar o PolicyStat ao Azure AD (Azure Active Directory).

A integração do PolicyStat ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao PolicyStat
- É possível permitir que os usuários se conectem automaticamente ao PolicyStat (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao PolicyStat, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do PolicyStat

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o PolicyStat por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-policystat-from-the-gallery"></a>Adicionando o PolicyStat por meio da galeria
Para configurar a integração do PolicyStat ao Azure AD, é necessário adicionar o PolicyStat à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o PolicyStat por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **PolicyStat**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_search.png)

5. No painel de resultados, selecione **PolicyStat** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o PolicyStat, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do PolicyStat é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PolicyStat.

No PolicyStat, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o PolicyStat, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do PolicyStat](#creating-a-policystat-test-user)** – para ter um equivalente de Brenda Fernandes no PolicyStat que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo PolicyStat.

**Para configurar o logon único do Azure AD com o PolicyStat, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **PolicyStat**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_samlbase.png)

3. Na seção **Domínio e URLs do PolicyStat**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.policystat.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do PolicyStat](http://www.policystat.com/support/) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_certificate.png) 

5. O objetivo desta seção é descrever como permitir que os usuários se autentiquem no PolicyStat com sua conta do AD do Azure usando federação baseada em protocolo SAML.

    O aplicativo PolicyStat espera que as declarações SAML estejam em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de **Atributos do Token SAML**.  

     As capturas de tela a seguir mostram um exemplo disso.

     ![Atributos](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_attribute.png "Atributos")

6. Para adicionar os mapeamentos de atributo necessários, execute as seguintes etapas:

    | Nome do atributo    |   Valor do atributo |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_04.png)

    ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. Na caixa de texto **Nome do Atributo**, digite **uid**.

    c. Na caixa de texto **Valor do Atributo**, selecione **ExtractMailPrefix()**.    
   
    d. Na lista **Email**, selecione **User.mail**.
    
    e. Clique em **Ok**

7. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/tutorial_general_400.png)

8. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do PolicyStat como administrador.

9. Clique na guia **Admin** e, em seguida, clique em **Configuração de Logon Único** no painel de navegação à esquerda.
   
    ![Menu Administrador](./media/active-directory-saas-policystat-tutorial/ic808633.png "Menu Administrador")

10. Na seção **Configuração**, selecione **Habilitar Integração de Logon Único**.
   
    ![Configuração de Logon Único](./media/active-directory-saas-policystat-tutorial/ic808634.png "Configuração de Logon Único")

11. Clique em **Configurar Atributos** e, em seguida, na seção **Configurar Atributos**, realize as seguintes etapas:
   
    ![Configuração de Logon Único](./media/active-directory-saas-policystat-tutorial/ic808635.png "Configuração de Logon Único")
   
    a. Na caixa de texto **Atributo do Nome de Usuário**, digite **uid**.

    b. Na caixa de texto **Atributo de Nome**, digite o **firstname** do usuário **Brenda**.

    c. Na caixa de texto **Atributo de Sobrenome**, digite o **lastname** do usuário **Fernandes**.

    d. Na caixa de texto **Atributo de Email**, digite o **emailaddress** do usuário **BrittaSimon@contoso.com**.

    e. Clique em **Salvar Alterações**.

12. Clique em **Seus Metadados do IDP** e, na seção **Seus Metadados do IDP**, realize as seguintes etapas:
   
    ![Configuração de Logon Único](./media/active-directory-saas-policystat-tutorial/ic808636.png "Configuração de Logon Único")
   
    a. Abra o arquivo de metadados baixado, copie o conteúdo e, depois, cole-o na caixa de texto **Metadados do Provedor de Identidade**.

    b. Clique em **Salvar Alterações**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-policystat-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-policystat-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-policystat-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-policystat-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-policystat-test-user"></a>Criando um usuário de teste do PolicyStat

Para permitir que os usuários do AD do Azure façam logon no PolicyStat, eles devem ser provisionados no PolicyStat.  

O PolicyStat dá suporte ao provisionamento de usuário just in time. Isso significa que não é necessário adicionar usuários manualmente ao PolicyStat. Os usuários serão adicionados automaticamente ao fazerem seu primeiro logon por meio do SSO.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do PolicyStat ou as APIs fornecidas pelo PolicyStat para provisionar contas de usuário do Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao PolicyStat.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao PolicyStat, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **PolicyStat**.

    ![Configurar Logon Único](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do PolicyStat no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo PolicyStat.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_203.png


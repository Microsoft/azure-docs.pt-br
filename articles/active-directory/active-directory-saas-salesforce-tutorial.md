---
title: "Tutorial: Integração do Azure Active Directory com o Salesforce | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 639e40ca7e406a1726033e9f5c5363c289087589
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Tutorial: Integração do Azure Active Directory com o Salesforce

Neste tutorial, você aprenderá a integrar o Salesforce ao Azure AD (Azure Active Directory).

A integração do Salesforce ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Salesforce
- Você pode permitir que seus usuários façam logon automaticamente no Salesforce (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Salesforce, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Salesforce com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Salesforce da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-salesforce-from-the-gallery"></a>Adicionar Salesforce da galeria
Para configurar a integração do Salesforce ao Azure AD, você precisará adicionar o Salesforce da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Salesforce na galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Salesforce**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. No painel de resultados, selecione **Salesforce** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Salesforce, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Salesforce é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Salesforce.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como sendo o valor de **Nome de Usuário** no Salesforce.

Para configurar e testar o logon único do Azure AD com o Salesforce, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Salesforce](#creating-a-salesforce-test-user)** – para ter um equivalente de Brenda Fernandes no Salesforce que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Salesforce.

**Para configurar o logon único do Azure AD com o Salesforce, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Salesforce**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Na seção **URLs e Domínio do Salesforce**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: 
   * Conta empresarial: `https://<subdomain>.my.salesforce.com`
   * Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > Esses não são os valores reais. Atualize esses valores com a URL de Logon real. Contate a [equipe de suporte do Cliente Salesforce](https://help.salesforce.com/support) para obter esses valores. 
 
4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. Na seção **configuração do Salesforce**, clique em **Configurar Salesforce** para abrir a janela **Configurar Logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.** 

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  Abra uma nova guia no navegador e faça logon em sua conta de administrador do Salesforce.

8.  No painel de navegação **Administrador**, clique em **Controles de Segurança** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  Na página **Configurações de Logon Único**, clique no botão **Editar**.
    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > Se não for possível habilitar as configurações de Logon Único para a conta do Salesforce, talvez seja necessário entrar em contato com a [equipe de suporte do Cliente Salesforce](https://help.salesforce.com/support). 

10. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

      ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. Para definir as configurações de logon único do SAML, clique em **Novo**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. Na página **Edição de Configuração de Logon Único do SAML** , realize as seguintes configurações:

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Para o campo **Nome** , digite um nome amigável para essa configuração. Fornecer um valor para **Nome** preenche automaticamente a caixa de texto **Nome da API**.

    b. Cole o valor **ID da Entidade de SAML** no campo **Emissor** em Salesforce.

    c. Na **caixa de texto Id da Entidade**, digite seu nome de domínio do Salesforce usando o seguinte padrão:
      
      * Conta empresarial: `https://<subdomain>.my.salesforce.com`
      * Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Clique em **Procurar** ou **Escolher Arquivo** para abrir a caixa de diálogo **Escolher Arquivo para Carregar**, selecione seu certificado do Salesforce e, em seguida, clique em **Abrir** para carregar o certificado.

    e. Para **Tipo de Identidade do SAML**, selecione **A declaração contém o nome de usuário salesforce.com do Usuário**.

    f. Para **Local de Identidade do SAML**, selecione **A identidade está no elemento NameIdentifier da instrução de assunto**

    g. Cole a **URL de Serviço de Logon Único** no campo **URL de Logon do Provedor de Identidade** no Salesforce.
    
    h. Para **Associação de Solicitação Iniciada do Provedor de Serviço**, selecione **Redirecionamento HTTP**.
    
    i. Finalmente, clique em **Salvar** para aplicar as configurações de logon único do SAML.

13. No painel de navegação à esquerda no Salesforce, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e, em seguida, clique em **Meu Domínio**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. Role para baixo até a seção **Configuração de Autenticação** e, em seguida, clique no botão **Editar**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. Na seção **Serviço de Autenticação**, selecione o nome amigável da sua configuração de SSO de SAML e, em seguida, clique em **Salvar**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se mais de um serviço de autenticação estiver selecionado, quando os usuários tentarem iniciar o logon único em seu ambiente do Salesforce, serão solicitados a selecionar o serviço de autenticação com o qual desejam entrar. Se você não quiser que isso aconteça, deverá **deixar todos os outros serviços de autenticação desmarcados**.
<CE>    
> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, simplesmente clique na guia **Logon Único** e acesse a documentação inserida através da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No painel de navegação esquerdo no **Portal do Azure**, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e Grupos** e clique em **Todos os Usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **Adicionar** para abrir a caixa de diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-salesforce-test-user"></a>Criar um usuário de teste do Salesforce

Nesta seção, é criado um usuário denominado Brenda Fernandes no Salesforce. O Salesforce dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.
Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce, um novo será criado quando você tentar acessar o Salesforce.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Salesforce.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Salesforce, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Salesforce**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Para testar suas configurações de logon único, abra o Painel de Acesso em [https://myapps.microsoft.com](https://myapps.microsoft.com/), depois entre na conta de teste e clique em **Salesforce**.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Provisionamento de Usuário](active-directory-saas-salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png


---
title: "Tutorial: integração do Azure Active Directory com a Área Restrita do Salesforce | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 90e08b9cf2feb93de4877bec9734352949896dca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Active Directory do Azure com a Área Restrita Salesforce

Neste tutorial, você aprende a integrar o Salesforce Sandbox ao Azure AD (Azure Active Directory).

A integração do Salesforce Sandbox ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Salesforce Sandbox
- É possível permitir que os usuários se conectem automaticamente ao Salesforce Sandbox (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Salesforce Sandbox, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Salesforce Sandbox

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Salesforce Sandbox por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionando o Salesforce Sandbox por meio da galeria
Para configurar a integração do Salesforce Sandbox ao Azure AD, é necessário adicionar o Salesforce Sandbox à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Salesforce Sandbox por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Salesforce Sandbox**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. No painel de resultados, selecione **Salesforce Sandbox** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Salesforce Sandbox, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Salesforce Sandbox é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Salesforce Sandbox.

Essa relação de vínculo é estabelecida com a atribuição do valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Salesforce Sandbox.

Para configurar e testar o logon único do Azure AD com o Salesforce Sandbox, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Salesforce Sandbox](#creating-a-salesforce-sandbox-test-user)** – para ter um equivalente de Brenda Fernandes no Salesforce Sandbox que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Salesforce Sandbox.

**Para configurar o logon único do Azure AD com o Salesforce Sandbox, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Salesforce Sandbox**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Na seção **Domínio e URLs do Salesforce Sandbox**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://<subdomain>.my.salesforce.com`

    > [!NOTE] 
    > Esse valor não é o real. Atualize esse valor com a URL de Logon real. Contate a [equipe de suporte ao Cliente do Salesforce Sandbox](https://help.salesforce.com/support) para obter esse valor.


4. Se já tiver configurado o logon único para outra instância de Área restrita do Salesforce em seu diretório, você também deve configurar o **Identificador** para ter o mesmo valor que a **URL de Logon**. 
    
    >[!Note]
    >O campo **Identificador** pode ser encontrado marcando a caixa de seleção **Mostrar configurações avançadas** na página **Configurar URL do Aplicativo** da caixa de diálogo 


5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado** e, em seguida, salve o arquivo de certificado no computador.

    ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. Na seção **Configuração do Salesforce Sandbox**, clique em **Configurar o Salesforce Sandbox** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. Abra uma nova guia no navegador e faça logon em sua conta Administrador do Salesforce Sandbox.

9. No menu na parte superior, clique em **Configuração**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. No painel de navegação à esquerda, clique em **Controles de Segurança** e clique em **Configurações de Logon Único**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. Na seção Configurações de Logon Único, realize as seguintes etapas: ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)
     
     a.  Selecione **SAML Habilitado**. 

     b.  Clique em **Novo**.

12. Na seção Configurações de Logon Único de SAML, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    a. Na caixa de texto Nome, digite o nome da configuração (por exemplo: *SPSSOWAAD\_Teste*). 

    b. Cole o valor da **ID da Entidade SAML** na caixa de texto **Emissor**.

    c. Na caixa de texto **ID da Entidade**, digite **https://test.salesforce.com** se essa for a primeira instância do Salesforce Sandbox que você está adicionando ao diretório. Se você já tiver adicionado uma instância da Área restrita do Salesforce, para a **ID da Entidade**, digite a **URL de Logon** que deve estar no seguinte formato: `http://company.my.salesforce.com`  
 
    d. Clique em **Procurar** para carregar o certificado baixado.  

    e. Para o **Tipo de Identidade SAML**, selecione **A declaração contém a ID de Federação do objeto de Usuário**.
 
    f. Para **Local de Identidade SAML**, selecione **A identidade está no elemento NameIdentifier da instrução Subject**.

    g. Cole a **URL do Serviço de Logon Único** na caixa de texto **URL de Logon do Provedor de Identidade**. 

    h. O SFDC não dá suporte a logout SAML.  Como alternativa, cole “https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0” na caixa de texto **URL de Logoff do Provedor de Identidade**.

    i. Para **Associação de Solicitação Iniciada pelo Provedor de Serviços**, selecione **HTTP Post**. 

    j. Clique em **Salvar**.

### <a name="enable-your-domain"></a>Habilitar seu domínio
Esta seção pressupõe que você já tenha criado um domínio.  Para obter mais informações, consulte [Definindo o nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Para habilitar seu domínio, execute as seguintes etapas:**

1. No painel de navegação esquerdo, clique em **Gerenciamento de Domínio** e clique em **Meu Domínio.**
   
     ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >Verifique se o domínio foi configurado corretamente. 

2. Na seção **Configurações de Página de Logon**, clique em **Editar** e, para o **Serviço de Autenticação**, selecione o nome da Configuração de Logon Único SAML da seção anterior e, por fim, clique em **Salvar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

Assim que você tiver um domínio configurado, seus usuários deverão usar a URL do domínio para fazer logon na área restrita Salesforce.  

Para obter o valor da URL, clique no perfil SSO criado na seção anterior.    

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. para exibir a lista de usuários, acesse **Usuários e grupos** e clique em **Todos os usuários**.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **Adicionar** para abrir a caixa de diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>Criando um usuário de teste do Salesforce Sandbox

Nesta seção, um usuário chamado Brenda Fernandes é criado no Salesforce Sandbox. O Salesforce Sandbox dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.
Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce Sandbox, um novo será criado quando você tentar acessar o Salesforce Sandbox.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Salesforce Sandbox.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Salesforce Sandbox, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Salesforce Sandbox**.

    ![Configurar Logon Único](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Se você quiser testar suas configurações de SSO, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Provisionamento de Usuário](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png


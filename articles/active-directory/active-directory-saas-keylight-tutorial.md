---
title: "Tutorial: Integração do Azure Active Directory ao LockPath Keylight | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o LockPath Keylight."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: e64a966f24411818abc4cc4ab29a428b5577d012
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Tutorial: Integração do Azure Active Directory ao LockPath Keylight

Neste tutorial, você aprende a integrar o LockPath Keylight ao Azure AD (Azure Active Directory).

A integração do LockPath Keylight ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao LockPath Keylight
- É possível permitir que os usuários se conectem automaticamente ao LockPath Keylight (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao LockPath Keylight, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do LockPath Keylight

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LockPath Keylight por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Adicionando o LockPath Keylight por meio da galeria
Para configurar a integração do LockPath Keylight ao Azure AD, é necessário adicionar o LockPath Keylight à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o LockPath Keylight por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **LockPath Keylight**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_search.png)

5. No painel de resultados, selecione **LockPath Keylight** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o LockPath Keylight, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LockPath Keylight é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LockPath Keylight.

No LockPath Keylight, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o LockPath Keylight, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do LockPath Keylight](#creating-a-lockpath-keylight-test-user)** – para ter um equivalente de Brenda Fernandes no LockPath Keylight que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo LockPath Keylight.

**Para configurar o logon único do Azure AD com o LockPath Keylight, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **LockPath Keylight**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_samlbase.png)

3. Na seção **Domínio e URLs do LockPath Keylight**, realize as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.keylightgrc.com/`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<company name>.keylightgrc.com`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Contate a [equipe de suporte ao Cliente do LockPath Keylight](https://www.lockpath.com/contact/) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo de certificado no computador.

    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/tutorial_general_400.png)
    
6. Na seção **Configuração do LockPath Keylight**, clique em **Configurar o LockPath Keylight** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_configure.png) 

7. Para habilitar o SSO no LockPath Keylight, realize as seguintes etapas:
   
    a. Faça logon em sua conta do LockPath Keylight como administrador.
    
    b. No menu na parte superior, clique em **Pessoa** e escolha **Instalação do Keylight**.
   
    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. No modo de exibição de árvore à esquerda, clique em **SAML**.
   
    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. Na caixa de diálogo **Configurações de SAML**, clique em **Editar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/404.png) 

8. Na página de caixa de diálogo **Editar Configurações de SAML** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    a. Defina **Autenticação SAML** como **Ativa**.

    b. Cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure na caixa de texto **URL de Logon do Provedor de Identidade**.

    c. Cole o valor da **URL do Serviço de Logoff Único** copiado do portal do Azure na caixa de texto **URL de Logoff do Provedor de Identidade**.

    d. Clique em **Escolher Arquivo** para selecionar o certificado do LockPath Keylight baixado e, depois, clique em **Abrir** para carregar o certificado.

    e. Defina **Local da ID de Usuário do SAML** como **Elemento NameIdentifier da declaração subject**.
    
    f. Forneça o **Provedor de Serviços do Keylight** usando o seguinte padrão: **https://&lt;CompanyName&gt;.keylightgrc.com**.
    
    g. Defina **Provisionar usuários automaticamente** como **Ativo**.

    h. Defina **Provisionar tipo de conta automaticamente** como **Usuário Completo**.

    i. Defina **Provisionar função de segurança automaticamente** e selecione **Usuário Padrão com SAML**.
    
    j. Defina **Provisionar configuração de segurança automaticamente** e selecione **Configuração de Usuário Padrão**.
     
    k. Na caixa de texto **Atributo de email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. Na caixa de texto **Atributo de nome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. Na caixa de texto **Atributo de sobrenome**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Criando um usuário de teste do LockPath Keylight

Nesta seção, você cria um usuário chamado Brenda Fernandes no LockPath Keylight. O LockPath Keylight dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário é criado ao acessar o LockPath Keylight, caso o usuário ainda não exista. 

>[!NOTE]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do LockPath Keylight](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao LockPath Keylight.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao LockPath Keylight, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **LockPath Keylight**.

    ![Configurar Logon Único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do LockPath Keylight no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo LockPath Keylight. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png


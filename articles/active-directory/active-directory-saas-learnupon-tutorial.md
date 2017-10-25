---
title: "Tutorial: integração do Azure Active Directory com o LearnUpon | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o LearnUpon."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: b6ac8acc244e9029be01ede5e0865c280171217d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Tutorial: Integração do Active Directory do Azure com o LearnUpon

Neste tutorial, você aprenderá a integrar o LearnUpon ao Azure AD (Azure Active Directory).

A integração do LearnUpon ao Azure AD oferece os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao LearnUpon
- Você pode habilitar que usuários façam logon automaticamente no LearnUpon (logon único) com as respectivas contas do AD do Azure
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao LearnUpon, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do LearnUpon habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LearnUpon da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-learnupon-from-the-gallery"></a>Adicionando o LearnUpon da galeria
Para configurar a integração do LearnUpon ao AD do Azure, você precisará adicionar o LearnUpon da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LearnUpon da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **LearnUpon**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_search.png)

5. No painel de resultados, selecione **LearnUpon** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o LearnUpon, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LearnUpon é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LearnUpon.

No LearnUpon, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o LearnUpon, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do LearnUpon](#creating-a-learnupon-test-user)** – para ter um equivalente de Brenda Fernandes no LearnUpon que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo LearnUpon.

**Para configurar o logon único do Azure AD com o LearnUpon, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **LearnUpon**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. Na seção **Domínio e URLs do LearnUpon**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_url.png)

    Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Observe que esse não é o valor real. você precisa atualizar esse valor com a URL de Resposta real. Para obter esse valor, entre em contato com a [equipe de suporte do LearnUpon](https://www.learnupon.com/features/support/).



4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Bruto)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do LearnUpon**, clique em **Configurar LearnUpon** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_configure.png) 

7. Abra outra instância do navegador e faça logon no LearnUpon com uma conta de administrador. 

8. Clique na guia **Configurações** .
   
    ![Configurar o logon único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png)

9. Clique em **Logon Único - SAML** e em **Configurações Gerais** para definir configurações de SAML.
   
    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 

10. Na seção **Configurações Gerais** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selecione **Habilitado**.

    b. Selecione **versão** como **2.0**.

    c. Selecione **Ignorar condições** como **Não**.

    d. Na caixa de texto **Nome do parâmetro Post de Token SAML**, digite o nome do parâmetro da solicitação post para a URL de consumidor SAML indicada acima, que contém a declaração SAML a ser verificada e autenticada. Por exemplo, **SAMLResponse**.

    e. Na caixa de texto **Formato de Nome de Identificador**, digite o valor que indica onde em sua Declaração SAML o identificador de usuários (endereço de Email) reside. Por exemplo: **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
  
    f. Na caixa de texto **Identificar o Local do Provedor**, digite o valor que indica para onde os usuários são enviados ao clicarem em seu ícone carregado na tela de logon do portal do Azure.
  
    g. Na caixa de texto **URL de Logoff**, cole a **URL de Saída** copiado do portal do Azure.
    
    h. Clique em **Gerenciar impressões digitais**e carregue a impressão digital do certificado baixado.

11. Clique em **Configurações de Usuário**e execute as seguintes etapas:
   
     ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. Na caixa de texto **Formato do Identificador de Nome**, digite o valor que indica onde na instrução de sua declaração do SAML reside o nome dos usuários. Por exemplo, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. Na caixa de texto **Formato do Identificador de Sobrenome**, digite o valor que indica onde na sua instrução de declaração do SAML reside o sobrenome dos usuários. Por exemplo, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-learnupon-test-user"></a>Criar um usuário de teste do LearnUpon

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no LearnUpon. O LearnUpon dá suporte ao provisionamento just-in-time, que é habilitado por padrão.

Não há itens de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o LearnUpon, caso ele ainda não exista. [Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Se precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do LearnUpon](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao LearnUpon.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao LearnUpon, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **LearnUpon**.

    ![Configurar Logon Único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco LearnUpon no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo LearnUpon.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png


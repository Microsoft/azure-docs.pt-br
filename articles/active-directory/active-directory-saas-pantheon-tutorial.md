---
title: "Tutorial: Integração do Azure Active Directory com o Pantheon | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Pantheon."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2c965d1-666f-44c2-b08f-b73163096374
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 3f4ac1db2ee83d9f9fcb375d0fb7c40ad21c4688
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pantheon"></a>Tutorial: Integração do Azure Active Directory com o Pantheon

Neste tutorial, você aprenderá a integrar o Pantheon ao Azure AD (Azure Active Directory).

A integração do Pantheon ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Pantheon
- Você pode habilitar os usuários a entrar automaticamente no Pantheon (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Pantheon, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Pantheon habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Pantheon da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-pantheon-from-the-gallery"></a>Adição do Pantheon da galeria
Para configurar a integração do Pantheon ao Azure AD, você precisa adicionar o Pantheon por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Pantheon da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Pantheon**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_search.png)

5. No painel de resultados, selecione **Pantheon** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Pantheon, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Pantheon é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Pantheon.

No Pantheon, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Pantheon, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Pantheon](#creating-a-pantheon-test-user)** – para ter um equivalente de Brenda Fernandes no Pantheon que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo do Pantheon.

**Para configurar o logon único do Azure AD com o Pantheon, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Pantheon**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_samlbase.png)

3. Na seção **URLs e Domínio do Pantheon**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `urn:auth0:pantheon:<orgname>-SSO`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://pantheon.auth0.com/login/callback?connection=<orgname>-SSO`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do Pantheon](https://pantheon.io/docs/getting-support/) para obter esses valores.

4. O aplicativo do Pantheon espera a asserção do SAML em um formato específico, o que exige a definição do valor do atributo UserIdentifier com o endereço de email do usuário. Por padrão, o Azure AD usa o UserPrincipalName para o atributo UserIdentifier. Porém, para uma integração bem-sucedida, você precisa ajustar esse valor para corresponder ao endereço de email do usuário. A integração funcionará apenas depois de concluir o mapeamento correto.

    ![Configurar Logon Único](./media/active-directory-saas-pantheon-tutorial/tutorial_attribute.png)  


5. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_certificate.png)

6. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-pantheon-tutorial/tutorial_general_400.png)

7. Na seção **Configuração do Pantheon**, clique em **Configurar o Pantheon** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_configure.png) 

8. Para configurar o logon único no lado do **Pantheon**, é necessário enviar o **Certificado** baixado e a **URL do Serviço de Logon Único do SAML** para a [equipe de suporte do Pantheon](https://pantheon.io/docs/getting-support/).

     > [!Note]
     > Você também precisa fornecer as informações de Domínios de Email e a Hora em que deseja habilitar esta conexão. Você pode encontrar mais detalhes sobre isso [aqui](https://pantheon.io/docs/sso-organizations/)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pantheon-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-pantheon-test-user"></a>Criação de um usuário de teste do Pantheon

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Pantheon. Siga as etapas a seguir para adicionar o usuário ao Pantheon. 

>[!NOTE] 
>Para o SSO funcionar, o usuário precisa ser criado primeiro no Pantheon.

1. Faça logon no Pantheon com credenciais de administrador.

2. Navegue até a página **Organização** do painel.
 
3. Clique em **Pessoas**.

4. Clique em **Adicionar usuário**.

5. Insira o endereço de email do usuário.

6. Escolha a função do usuário.

7. Clique em **Adicionar usuário**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Pantheon.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Pantheon, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Pantheon**.

    ![Configurar Logon Único](./media/active-directory-saas-pantheon-tutorial/tutorial_pantheon_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Pantheon no Painel de Acesso, deverá fazer logon automaticamente no seu aplicativo do Pantheon.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pantheon-tutorial/tutorial_general_203.png


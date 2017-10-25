---
title: "Tutorial: Integração do Azure Active Directory ao LCVista | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o LCVista."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8db80d6e-3275-419f-aa39-6115a7bc9800
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: c19f81da495eb7116b62797d1755d312a23f3805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lcvista"></a>Tutorial: Integração do Azure Active Directory ao LCVista

Neste tutorial, você aprenderá a integrar o LCVista ao Azure AD (Active Directory).

A integração do LCVista ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao LCVista
- Você pode habilitar os usuários a entrar automaticamente no LCVista (Logon Único) com as respectivas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao LCVista, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do LCVista

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LCVista usando a galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-lcvista-from-the-gallery"></a>Adicionando o LCVista usando a galeria
Para configurar a integração do LCVista ao Azure AD, você precisa adicionar o LCVista da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LCVista da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **LCVista**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_search.png)

5. No painel de resultados, selecione **LCVista** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você vai configurar e testar o logon único do Azure AD com o LCVista, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do LCVista é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do LCVista.

Essa relação de vinculação é estabelecida pela atribuição do valor de **nome de usuário** no Azure AD como o valor do **Nome de usuário** no LCVista.

Para configurar e testar o logon único do Azure AD com o LCVista, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do LCVista](#creating-a-lcvista-test-user)** – para ter um equivalente de Brenda Fernandes no LCVista que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo LCVista.

**Para configurar o logon único do Azure AD com o LCVista, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **LCVista**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_samlbase.png)

3. Na seção **Domínio e URLs do LCVista**, execute as seguintes etapas:

    ![Configurar o logon único](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.lcvista.com/rainier/login`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.lcvista.com` 
     
    > [!NOTE] 
    > Esses não são os valores reais. Atualize esses valores com o Identificador e a URL de Logon reais. Contate a [equipe de suporte do Cliente LCVista](https://lcvista.com/contact) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/active-directory-saas-lcvista-tutorial/tutorial_general_400.png)
    
6. Na seção **Configuração do LCVista**, clique em **Configurar o LCVista** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_configure.png) 

7.  Entre no seu aplicativo LCVista como um administrador.

8. Na seção **Configuração de SAML**, marque **Habilitar logon de SAML** e digite os detalhes como mencionado na imagem abaixo. 

    ![Configurar o logon único](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_config.png)

    a. Cole a **URL do Emissor** que você copiou do Azure AD na seção **ID da Entidade**. 

    b. Cole a **URL do Serviço de Logon Único** que você copiou do Azure AD na seção **URL**.

    c. Em Metadados (XML) que você baixou do Portal do Azure, copie o valor **X509Certificate** e cole-o na seção **Certificado x509**.

    d. Na caixa de texto **Atributo de nome**, cole o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Na caixa de texto **Atributo de sobrenome**, cole o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Na caixa de texto **Atributo de email**, cole o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Na caixa de texto **Atributo de nome de usuário**, cole o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Clique em **Salvar** para salvar as configurações.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lcvista-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lcvista-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lcvista-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-lcvista-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-lcvista-test-user"></a>Criando um usuário de teste do LCVista

Nesta seção, você criará um usuário chamado Brenda Fernandes no LCVista. É preciso contatar a [equipe de suporte do Cliente LCVista](https://lcvista.com/contact) para adicionar os usuários no aplicativo LCVista. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao LCVista.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao LCVista, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **LCVista**.

    ![Configurar Logon Único](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso. Clique no bloco LCVista no Painel de Acesso e você será redirecionado para a página de logon Organização. Após fazer logon com êxito, você entrará no aplicativo LCVista. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_203.png


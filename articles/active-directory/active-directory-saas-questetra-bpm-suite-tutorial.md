---
title: "Tutorial: Integração do Azure Active Directory com o Questetra BPM Suite | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Questetra BPM Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 89d1ebc090a7b667885f5dcbab14312efd767f0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração do Active Directory do Azure com o Questetra BPM Suite

Neste tutorial, você aprenderá a integrar o Questetra BPM Suite ao Azure AD (Azure Active Directory).

A integração do Questetra BPM Suite ao Azure AD oferece os seguintes benefícios:

- Você pode controlar, no AD do Azure, quem tem acesso ao Questetra BPM Suite
- Você pode habilitar seus usuários a fazerem logon automaticamente no Questetra BPM Suite (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o Questetra BPM Suite, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Questetra BPM Suite

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar um Questetra BPM Suite da galeria
2. Configurar e testar logon único do Azure AD

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Adicionar um Questetra BPM Suite da galeria
Para configurar a integração do Questetra BPM Suite com o AD do Azure, você precisa adicionar o Questetra BPM Suite, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Questetra BPM Suite por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Questetra BPM Suite**, selecione **Questetra BPM Suite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adicionar da galeria](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o Questetra BPM Suite, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Questetra BPM Suite é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado do Questetra BPM Suite.

No Questetra BPM Suite, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o Questetra BPM Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)** - para ter um equivalente de Brenda Fernandes no Questetra BPM Suite que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo Questetra BPM Suite.

**Para configurar o logon único do AD do Azure com o Questetra BPM Suite, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Questetra BPM Suite**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Logon único baseado em SAML](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. Na seção **URLs e Domínio do Questetra BPM Suite**, execute as seguintes etapas:

    ![Seção Domínio e URLs do Questetra BPM Suite](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Você pode obter esses valores na seção **Informações de SP** no site de empresa de **Questetra BPM Suite**, o que é explicado posteriormente no tutorial, ou entre em contato com a [equipe de suporte ao cliente do Questetra BPM Suite](https://www.questetra.com/contact/). 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

    ![Seção Certificado de Autenticação SAML](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Questetra BPM Suite**, clique em **Configurar o Questetra BPM Suite** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Seção de configuração do Questetra BPM Suite](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. Em outra janela do navegador da Web, faça logon em seu site de empresa **Questetra BPM Suite** como administrador.

8. No menu na parte superior, clique em **Configurações do Sistema**. 
   
    ![Logon Único do AD do Azure][10]

9. Para abrir a página **SingleSignOnSAML**, clique em **SSO (SAML)**. 
   
    ![Logon Único do AD do Azure][11]

10. No site de empresa do **Questetra BPM Suite**, na seção **Informações de SP**, execute as etapas a seguir:

    a. Copie a **URL do ACS** e cole-a na caixa de texto **URL de Logon** na seção **Domínio e URLs do Questetra BPM Suite** no Portal do Azure.
    
    b. Copie a **ID da entidade** e cole-a na caixa de texto **Identificador**, na seção **Domínio e URLs do Questetra BPM Suite** no Portal do Azure.

11. No seu site de empresa **Questetra BPM Suite**, execute as seguintes etapas: 
   
    ![Configurar Logon Único][15]
   
    a. Selecione **Habilitar Logon Único**.
   
    b. Na caixa de texto **ID da Entidade**, cole o valor da **ID da Entidade do SAML** copiado no Portal do Azure.
    
    c. Na caixa de texto **URL da página de entrada**, cole o valor da **URL do Serviço de Logon Único do SAML** copiado do Portal do Azure.
    
    d. Na caixa de texto **URL da página de saída**, cole o valor da **URL de Saída** copiado do Portal do Azure.
    
    e. Na caixa de texto **Formato de NameID**, digite `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Abra o certificado codificado em **Base64** no bloco de notas baixado do Portal do Azure, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado de validação**. 

    g. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Criar um usuário de teste do Questetra BPM Suite

O objetivo desta seção é criar um usuário chamado Britta Simon no Questetra BPM Suite.

**Para criar um usuário chamado Brenda Fernandes no Questetra BPM Suite, execute as seguintes etapas:**

1. Faça logon no site de sua empresa do Questetra BPM Suite como administrador.
2. Vá para **Configurações do Sistema > Lista de Usuários > Novo Usuário**. 
3. Na caixa de diálogo Novo Usuário, execute as seguintes etapas: 
   
    ![Criar um usuário de teste][300] 
   
    a. Na caixa de texto **Nome**, digite o **nome** do usuário **britta.simon@contoso.com**.
   
    b. Na caixa de texto **Email**, digite o **email** do usuário **britta.simon@contoso.com**
   
    c. Na caixa de texto **Senha** , digite uma **senha** para o usuário.
    
    d. Clique em **Adicionar novo usuário**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Questetra BPM Suite.

![Atribuir usuário][200] 

**Para atribuir Britta Simon ao Questetra BPM Suite, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Questetra BPM Suite**.

    ![Questetra BPM Suite na lista de aplicativos](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clica no bloco Questetra BPM Suite no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Questetra BPM Suite.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 


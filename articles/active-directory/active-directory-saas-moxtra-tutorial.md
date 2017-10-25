---
title: "Tutorial: Integração do Azure Active Directory com o Moxtra | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Moxtra."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: db2f041a44b6771b0a4f734e58d899298ef0847b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Tutorial: integração do Active Directory do Azure ao Moxtra

Neste tutorial, você aprenderá a integrar o Moxtra ao Azure AD (Azure Active Directory).

A integração do Moxtra ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao Moxtra
- Você pode permitir que os usuários façam logon automaticamente no Moxtra (Logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Moxtra, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Moxtra

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Moxtra da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-moxtra-from-the-gallery"></a>Adicionando Moxtra da galeria
Para configurar a integração do Moxtra ao Azure AD, você precisará adicionar o Moxtra da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Moxtra a partir da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Moxtra**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_search.png)

5. No painel de resultados, selecione **Moxtra** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Moxtra, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Moxtra é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Moxtra.

No Moxtra, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Moxtra, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste Moxtra](#creating-a-moxtra-test-user)**: para ter um equivalente de Brenda Fernandes no Moxtra que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Moxtra.

**Para configurar o logon único do Azure AD com o Moxtra, realize as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Moxtra**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_samlbase.png)

3. Na seção **URLs e Domínio do Moxtra**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL como: `https://www.moxtra.com/service/#login`

4. O aplicativo Moxtra espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo dessa configuração. 

    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_attributes.png)
    
5. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | nome | user.givenname |
    | sobrenome | user.surname |
    | idpid    | <ID da Entidade SAML> 

    > [!Note]
    > O valor do atributo **idpid** não é real. Você pode obter o valor real da seção **Referência rápida** na **Configuração Moxtra**.
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_04.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    ![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_attribute_05.png)

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Clique em **OK**.
    
5. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_certificate.png) 

6. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_400.png)

7. Na seção **Configuração do Moxtra**, clique em **Configurar o Moxtra** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único do SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_configure.png) 

8. Em outra janela do navegador, entre em seu site de empresa do Moxtra como administrador.

9. Na barra de ferramentas à esquerda, clique em **Console do Administrador > Logon Único do SAML** e clique em **Novo**.
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 

10. Na página **SAML** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Na caixa de texto **Nome** , digite um nome para a sua configuração (por exemplo: *SAML*). 
  
    b. Na caixa de texto **ID da Entidade IdP**, cole o valor da **ID da Entidade SAML** copiado no Portal do Azure. 
 
    c. Na caixa de texto **URL de Logon**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do Portal do Azure. 
 
    d. Na caixa de texto **AuthnContextClassRef**, digite **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
 
    e. Na caixa de texto **Formato da NameID**, digite **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**. 
 
    f. Abra o certificado baixado no bloco de notas do Portal do Azure, copie o conteúdo e cole-o na caixa de texto **Certificado**.    
 
    g. Na caixa de texto de domínio de email SAML email, digite seu domínio de email SAML.    
  
    >[!NOTE]
    >Para ver as etapas de verificação do domínio, clique no “**i**” abaixo.

    h. Clique em **Atualizar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-moxtra-test-user"></a>Criar um usuário de teste Moxtra

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Moxtra.

**Para criar um usuário chamado Brenda Fernandes no Moxtra, realize as seguintes etapas:**

1. Faça logon em seu site de empresa do Moxtra como administrador.

2. Na barra de ferramentas à esquerda, clique em **Console do Administrador > Gerenciamento de Usuário** e em **Adicionar Usuário**.
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 

3. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
  
    a. Na caixa de texto **Nome**, digite **Brenda**.
  
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  
    c. Na caixa de texto **Email**, digite o endereço de email de Brenda no Portal do Azure.
  
    d. Na caixa de texto **Divisão**, digite **Dev**.
  
    e. Na caixa de texto **Departamento**, digite **TI**.
  
    f. Selecione **Administrator**.
  
    g. Clique em **Adicionar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao Moxtra.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Moxtra, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Moxtra**.

    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Moxtra no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do Moxtra.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png


---
title: "Tutorial: integração do Azure Active Directory com o DocuSign | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o DocuSign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 29c99fdf39d366df90abc070f7b836320935035c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: integração do Active Directory do Azure com o DocuSign

Neste tutorial, você aprende a integrar o DocuSign ao Azure AD (Azure Active Directory).

A integração do DocuSign ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao DocuSign
- É possível permitir que os usuários se conectem automaticamente ao DocuSign (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao DocuSign, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do DocuSign

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o DocuSign por meio da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-docusign-from-the-gallery"></a>Adicionando o DocuSign por meio da galeria
Para configurar a integração do DocuSign ao Azure AD, é necessário adicionar o DocuSign à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o DocuSign por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Docusign**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. No painel de resultados, selecione **DocuSign** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o DocuSign, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do DocuSign é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do DocuSign.

Essa relação de vínculo é estabelecida com a atribuição do valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no DocuSign.

Para configurar e testar o logon único do Azure AD com o DocuSign, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do DocuSign](#creating-a-docusign-test-user)** – para ter um equivalente de Brenda Fernandes no DocuSign que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo DocuSign.

**Para configurar o logon único do Azure AD com o DocuSign, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **DocuSign**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo de certificado no computador.

    ![Configurar Logon Único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. Na seção **Configuração do DocuSign** do portal do Azure, clique em **Configurar o DocuSign** para abrir a janela Configurar logon. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**
    
    ![Configurar Logon Único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. Em outra janela do navegador da Web, faça logon no **portal de administração do DocuSign** como administrador.

6. No menu de navegação à esquerda, clique em **Domínios**.
   
    ![Configurando o logon único][51]

7. No painel direito, clique em **Solicitar Domínio**.
   
    ![Configurando o logon único][52]

8. Na caixa de diálogo **Solicitar um domínio**, na caixa de texto **Nome de Domínio**, digite o domínio de sua empresa e clique em **Solicitar**. Verifique o domínio e confira se o status está ativo.
   
    ![Configurando o logon único][53]

9. No menu à esquerda, clique em **Provedores de Identidade**  
   
    ![Configurando o logon único][54]
10. No painel direito, clique em **Adicionar Provedor de Identidade**. 
   
    ![Configurando o logon único][55]

11. Na página **Configurações do Provedor de Identidade** , execute as seguintes etapas:
   
    ![Configurando o logon único][56]

    a. Na caixa de texto **Nome** , digite um nome exclusivo para sua configuração. Não use espaços.

    b. Cole a **ID da Entidade SAML** na caixa de texto **Emissor do Provedor de Identidade**.

    c. Cole a **URL do Serviço de Logon Único SAML** na caixa de texto **URL de Logon do Provedor de Identidade**.

    d. Cole a **URL de Saída** na caixa de texto **URL de Logoff do Provedor de Identidade**.

    e. Selecione **Assinar Solicitação AuthN**.

    f. Para **Enviar solicitação AuthN por**, selecione **POST**.

    g. Em **Enviar solicitação de logoff por**, selecione **GET**.

12. Na seção **Mapeamento de Atributo Personalizado** , escolha o campo que você deseja mapear com a Declaração do Azure AD. Neste exemplo, a declaração **emailaddress** é mapeada com o valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Esse é o nome de declaração padrão do Azure AD para a declaração de email. 
   
    > [!NOTE]
    > Use o **Identificador de usuário** apropriado para mapear o usuário do Azure AD para o mapeamento de usuário do DocuSign. Selecione o campo adequado e insira o valor apropriado com base nas configurações de sua organização.
          
    ![Configurando o logon único][57]

13. Na seção **Certificado do Provedor de Identidade**, clique em **Adicionar Certificado** e, depois, carregue o certificado baixado no portal do Azure AD.   
   
    ![Configurando o logon único][58]

14. Clique em **Salvar**.

15. Na seção **Provedores de Identidade**, clique em **Ações** e em **Pontos de Extremidade**.   
   
    ![Configurando o logon único][59]
 
16. Na seção **Exibir Pontos de Extremidade do SAML 2.0** do **portal de administração do DocuSign**, realize as seguintes etapas:
   
    ![Configurando o logon único][60]
   
    a. Copie a **URL de Emissor do Provedor de Serviço** e, em seguida, cole-a na caixa de texto **Identificador** na seção **Domínio e URLs do DocuSign** do portal do Azure seguindo o padrão: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Copie a **URL de Logon do Provedor de Serviço** e, em seguida, cole-a na caixa de texto **URL de Logon** na seção **Domínio e URLs do DocuSign** do portal do Azure seguindo o padrão: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/`.

    ![Configurar Logon Único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Clique em **Fechar**
    
17. No portal do Azure, clique em **Salvar**.
    
    ![Configurar Logon Único](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **Adicionar** para abrir a caixa de diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-docusign-test-user"></a>Criando um usuário de teste do DocuSign

O aplicativo dá suporte ao **provisionamento de usuário Just-In-Time** e, após a autenticação, os usuários são criados no aplicativo automaticamente.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao DocuSign.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao DocuSign, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **DocuSign**.

    ![Configurar Logon Único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do DocuSign no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo DocuSign.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar Provisionamento de Usuário](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png


---
title: "Tutorial: Integração do Azure Active Directory ao Litmos | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Litmos."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: ef1b5860ba0a406022bbd11afb366d14bee2c57d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Tutorial: Integração do Active Directory do Azure ao Litmos

Neste tutorial, você aprende a integrar o Litmos ao Azure AD (Azure Active Directory).

A integração do Litmos ao Azure AD proporciona os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Litmos.
- É possível permitir que os usuários se conectem automaticamente ao Litmos (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure ao Litmos, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Litmos

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Litmos da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-litmos-from-the-gallery"></a>Adicionando o Litmos da galeria
Para configurar a integração do Litmos ao AD do Azure, você precisará adicionar o Litmos da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Litmos da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Litmos**, selecione **Litmos** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Litmos na lista de resultados](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Litmos, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Litmos é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Litmos.

No Litmos, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o Litmos, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Litmos](#create-a-litmos-test-user)** – para ter um equivalente de Brenda Fernandes no Litmos que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Litmos.

**Para configurar o logon único do AD do Azure com o Litmos, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Litmos**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_samlbase.png)

3. Na seção **Domínio e URLs do Litmos**, realize as seguintes etapas:

    ![Informações de logon único em Domínio e URLs do Litmos](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.litmos.com/account/Login`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais, que são explicados adiante no tutorial ou contate a [equipe de suporte do Litmos](https://www.litmos.com/contact-us/) para obter esses valores.

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_certificate.png)

5. Como parte da configuração, você precisa personalizar os **Atributos de Token SAML** para seu aplicativo Litmos.

    ![Seção Atributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute.png)
           
    | Nome do atributo   | Valor do atributo |   
    | ---------------  | ----------------|
    | Nome |user.givenname |
    | Sobrenome  |user.surname |
    | Email |user.mail |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Adicionar atributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_04.png)

    ![Caixa de diálogo Adicionar atributo](./media/active-directory-saas-litmos-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.     

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-litmos-tutorial/tutorial_general_400.png)

7. Em outra janela do navegador, entre em seu site de empresa do Litmos como administrador.

8. Na barra de navegação à esquerda, clique em **Contas**.
   
    ![Seção Contas no lado do aplicativo][22] 

9. Clique na guia **Integrações** .
   
    ![Guia Integração][23] 

10. Na guia **Integrações**, role para baixo até **Integrações de Terceiros** e clique na guia **SAML 2.0**.
   
    ![Seção SAML 2.0][24] 

11. Copie o valor em **O ponto de extremidade SAML do Litmos é:** e cole-o na caixa de texto **URL de Resposta** na seção **Domínio e URLs do Litmos** do portal do Azure. 
   
    ![Ponto de extremidade SAML][26] 

12. No seu aplicativo **Litmos** , execute as seguintes etapas:
    
     ![Aplicativo Litmos][25] 
     
     a. Clique em **Habilitar SAML**.
    
     b. Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado SAML X.509** .
     
     c. Clique em **Salvar Alterações**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-litmos-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-litmos-test-user"></a>Criar um usuário de teste do Litmos

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Litmos.  
O aplicativo Litmos oferece suporte ao provisionamento Just-in-Time. Isso significa que, se for necessário, uma conta de usuário será criada automaticamente durante uma tentativa de acessar o aplicativo usando o Painel de Acesso.

**Para criar um usuário chamado Brenda Fernandes no Litmos, execute as seguintes etapas:**

1. Em outra janela do navegador, entre em seu site de empresa do Litmos como administrador.

2. Na barra de navegação à esquerda, clique em **Contas**.
   
    ![Seção Contas no lado do aplicativo][22] 

3. Clique na guia **Integrações** .
   
    ![Guia Integrações][23] 

4. Na guia **Integrações**, role para baixo até **Integrações de Terceiros** e clique na guia **SAML 2.0**.
   
    ![SAML 2.0][24] 
    
5. Selecione **Gerar Usuários Automaticamente**
   
    ![Gerar usuários automaticamente][27] 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Litmos.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Litmos, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Litmos**.

    ![O link do Litmos na lista de Aplicativos](./media/active-directory-saas-litmos-tutorial/tutorial_litmos_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  

Ao clicar no bloco Litmos no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Litmos. 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png


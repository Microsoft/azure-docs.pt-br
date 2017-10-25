---
title: "Tutorial: Integração do Azure Active Directory ao Bonusly | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Bonusly."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 29a88b2efdb9f0f33f7933bc654a5a0fdf589c5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: integração do Azure Active Directory ao Bonusly

Neste tutorial, você aprenderá a integrar o Bonusly ao Azure AD (Azure Active Directory).

A integração do Bonusly ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Bonusly
- Você pode permitir que seus usuários façam logon automaticamente no Bonusly (Logon único) com as contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Bonusly, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Bonusly habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Bonusly da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-bonusly-from-the-gallery"></a>Adicionando o Bonusly da galeria
Para configurar a integração do Bonusly ao Azure AD, você precisa adicionar o Bonusly da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Bonusly da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Bonusly**, selecione **Bonusly** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Bonusly na lista de resultados](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o Bonusly, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Bonusly é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Bonusly.

No Bonusly, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Bonusly, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Bonusly](#create-a-bonusly-test-user)** – para ter um equivalente de Brenda Fernandes no Bonusly vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Testar o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Bonusly.

**Para configurar o logon único do Azure AD com o Bonusly, realize as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Bonusly**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_samlbase.png)

3. Na seção **Domínio e URLs do Bonusly**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_url.png)

    Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com a URL de Resposta real. Contate a [equipe de suporte do Bonusly](https://Bonusly/contact) para obter o valor.
 
4. Na seção **Certificado de Autenticação SAML**, copie o valor da **IMPRESSÃO DIGITAL** do certificado.

    ![O link de download do Certificado](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-bonus-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Bonusly**, clique em **Configurar o Bonusly** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configuração do Bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_configure.png) 

7. Em outra janela do navegador, faça logon no seu locatário do **Bonusly**.

8. Na barra de ferramentas na parte superior, clique em **Configurações** e selecione **Integrações e aplicativos**.
   
    ![Seção Social Bonusly](./media/active-directory-saas-bonus-tutorial/ic773686.png "Bonusly")
9. Em **Logon Único**, selecione **SAML**.

10. Na página do diálogo **SAML** , realize as seguintes etapas:
   
    ![Página de Diálogo Saml do Bonusly](./media/active-directory-saas-bonus-tutorial/ic773687.png "Bonusly")
   
    a. Na caixa de texto **URL de destino do SSO de IdP**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do Portal do Azure.
   
    b. Na caixa de texto **Emissor IdP**, cole o valor da **ID de Entidade do SAML** que você copiou do Portal do Azure. 

    c. Na caixa de texto **URL de Logon do IdP**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do Portal do Azure.

    d. Cole o valor da **Impressão digital** copiado do Portal do Azure na caixa de texto **Impressão Digital do Certificado**.
   
11. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-bonus-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-bonus-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![O botão Adicionar](./media/active-directory-saas-bonus-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![A caixa de diálogo Usuário](./media/active-directory-saas-bonus-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-bonusly-test-user"></a>Criar um usuário de teste do Bonusly

Para permitir que os usuários do Azure AD façam logon no Bonusly, eles devem estar provisionados no Bonusly. No caso do Bonusly, o provisionamento é uma tarefa manual.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Bonusly ou as APIs fornecidas pelo Bonusly para provisionar as contas de usuário do AAD.
>  

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Em uma janela do navegador da Web, faça logon no seu locatário do Bonusly.

2. Clique em **Configurações**.
 
    ![Configurações](./media/active-directory-saas-bonus-tutorial/ic781041.png "Configurações")

3. Clique na guia **Usuários e bônus** .
   
    ![Usuários e bônus](./media/active-directory-saas-bonus-tutorial/ic781042.png "Usuários e bônus")

4. Clique em **Gerenciar Usuários**.
   
    ![Gerenciar Usuários](./media/active-directory-saas-bonus-tutorial/ic781043.png "Gerenciar Usuários")

5. Clique em **Adicionar Usuário**.
   
    ![Adicionar Usuário](./media/active-directory-saas-bonus-tutorial/ic781044.png "Adicionar Usuário")

6. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
   
    ![Adicionar Usuário](./media/active-directory-saas-bonus-tutorial/ic781045.png "Adicionar Usuário")  

    a. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.
 
    c. Na caixa de texto **Email**, insira o email do usuário, como **brittasimon@contoso.com**.

    d. Clique em **Salvar**.
   
     >[!NOTE]
     >O titular da conta do Azure AD recebe um email com um link de confirmação de conta antes que ela se torne ativa.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Bonusly.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Bonusly, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha o **Bonusly**.

    ![O link do Bonusly na lista de Aplicativos](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Bonusly no Painel de Acesso, deverá ser automaticamente conectado ao seu aplicativo Bonusly.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_203.png


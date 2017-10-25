---
title: "Tutorial: integração do Azure Active Directory com o ZPA (Zscaler Private Access) | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o ZPA (Zscaler Private Access)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 5c598bfa5b6725d21a89df54dbcb3314cc631d80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Tutorial: integração do Azure Active Directory com o ZPA (Zscaler Private Access)

Neste tutorial, você aprenderá a integrar o ZPA (Zscaler Private Access) ao Azure AD (Azure Active Directory).

A integração do ZPA (Zscaler Private Access) ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao ZPA (Zscaler Private Access)
- Você pode permitir que seus usuários faça logon automaticamente (logon único) no ZPA (Zscaler Private Access) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ZPA (Zscaler Private Access), você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura com logon único do ZPA (Zscaler Private Access) habilitado


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando ZPA (Zscaler Private Access) da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Adicionando ZPA (Zscaler Private Access) da galeria
Para configurar a integração do ZPA (Zscaler Private Access) ao Azure AD, você precisará adicionar o ZPA (Zscaler Private Access) da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ZPA (Zscaler Private Access) da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **ZPA (Zscaler Private Access)**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

5. No painel de resultados, selecione **ZPA (Zscaler Private Access)** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o ZPA (Zscaler Private Access), com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ZPA (Zscaler Private Access) é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ZPA (Zscaler Private Access).

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no ZPA (Zscaler Private Access).

Para configurar e testar o logon único do Azure AD com o ZPA (Zscaler Private Access), você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criando um usuário de teste do ZPA (Zscaler Private Access)](#creating-a-zscaler-private-access-(zpa)-test-user)** – para ter um equivalente de Brenda Fernandes no ZPA (Zscaler Private Access) que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo ZPA (Zscaler Private Access).

**Para configurar o logon único do Azure AD com o ZPA (Zscaler Private Access), execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração do aplicativo do **ZPA (Zscaler Private Access)**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
3. Na seção **URLs e Domínio do ZPA (Zscaler Private Access)**, execute as seguintes etapas:
    
    ![Configurar Logon Único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. Na caixa de texto **Identificador**, digite: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Observe que esses não são os valores reais. É necessário atualizar esses valores com a URL de Entrada e o Identificador reais. Aqui, sugerimos que você use o valor exclusivo de URL no Identificador. Entre em contato com [equipe de suporte do ZPA (Zscaler Private Access)](https://help.zscaler.com/zpa-submit-ticket) para obter esses valores.

4. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar o logon único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_400.png)   

5. Na caixa de diálogo **Criar um Novo Certificado**, clique no ícone de calendário e selecione uma **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar Logon Único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_500.png)

6. Na seção **Certificado de Autenticação SAML**, selecione **Ativar o novo certificado** e clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

7. Na janela pop-up **Certificado de substituição**, clique em **OK**.

    ![Configurar o logon único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_600.png)

8. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

9. Em uma janela diferente do navegador da Web, faça logon no site ZPA (Zscaler Private Access) da sua empresa como administrador.

10. Navegue até **Administrador** e, em seguida, clique em **Configuração de IdP**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

11. Na seção **Configuração IdP**, clique em **Adicionar Nova Configuração de IdP**.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

12. Na seção **Nova Configuração de IdP**, execute as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Clique em **Selecionar Arquivo** e carregue o arquivo de metadados baixado.

    b. Clique no botão **Salvar** .
    


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Criar um usuário de teste do ZPA (Zscaler Private Access)

Nesta seção, você deve criar uma usuária chamada Brenda Fernandes no ZPA (Zscaler Private Access). Trabalhe com [equipe de suporte do ZPA (Zscaler Private Access)](https://help.zscaler.com/zpa-submit-ticket) para adicionar os usuários na plataforma ZPA (Zscaler Private Access).


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao ZPA (Zscaler Private Access).

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao ZPA (Zscaler Private Access), execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **ZPA (Zscaler Private Access)**.

    ![Configurar Logon Único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ZPA (Zscaler Private Access) no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do ZPA (Zscaler Private Access).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_203.png
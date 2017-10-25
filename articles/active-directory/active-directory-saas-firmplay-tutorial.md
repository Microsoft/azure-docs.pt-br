---
title: "Tutorial: Integração do Azure Active Directory com o FirmPlay - Employee Advocacy for Recruiting | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o FirmPlay - Employee Advocacy for Recruiting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: 3cddd5b9508159089bf344dbb3882d462799747c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Tutorial: Integração do Azure Active Directory com FirmPlay - Employee Advocacy for Recruiting

Neste tutorial, você aprenderá a integrar o FirmPlay - Employee Advocacy for Recruiting com o Azure Active Directory (Azure AD).

Integrar o FirmPlay - Employee Advocacy for Recruiting com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao FirmPlay - Employee Advocacy for Recruiting
- Você pode habilitar os usuários para fazerem logon automaticamente no FirmPlay - Employee Advocacy for Recruiting (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com FirmPlay - Employee Advocacy for Recruiting, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada com logon único do FirmPlay - Employee Advocacy for Recruiting


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o FirmPlay - Employee Advocacy for Recruiting a partir da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Adicionando o FirmPlay - Employee Advocacy for Recruiting a partir da galeria
Para configurar a integração do FirmPlay - Employee Advocacy for Recruiting no Azure AD, você precisa adicionar o FirmPlay na galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FirmPlay - Employee Advocacy for Recruiting na galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **FirmPlay - Employee Advocacy for Recruiting**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_001.png)

5. No painel de resultados, selecione **FirmPlay - Employee Advocacy for Recruiting** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você irá configurar e testar o logon único do Azure AD com o FirmPlay - Employee Advocacy for Recruiting com base em um usuário de teste denominado "Brenda Fernandes".

Para o logon único funcionar, o Azure AD precisa saber qual usuário no FirmPlay - Employee Advocacy for Recruiting é equivalente a um usuário no Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no FirmPlay - Employee Advocacy for Recruiting.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no FirmPlay - Employee Advocacy for Recruiting.

Para configurar e testar o logon único do Azure AD com o FirmPlay - Employee Advocacy for Recruiting, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste FirmPlay - Employee Advocacy for Recruiting](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)** - para ter um equivalente de Brenda Fernandes no FirmPlay vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal de Gerenciamento do Azure e irá configurar o logon único no aplicativo FirmPlay - Employee Advocacy for Recruiting.

**Para configurar o logon único do Azure AD com o FirmPlay - Employee Advocacy for Recruiting, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração do aplicativo **FirmPlay - Employee Advocacy for Recruiting**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_01.png)

3. Na seção **Domínio e URLs do FirmPlay - Employee Advocacy for Recruiting** seção, na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:`https://<your-subdomain>.firmplay.com/`

    ![Configurar Logon Único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Observe que esse não é o valor real. Você precisa atualizar esse valor com a URL de Entrada real. Entre em contato com a [equipe de suporte do FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) para obter esse valor. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Criar novo certificado**.

    ![Configurar o logon único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_03.png)   

5. Na caixa de diálogo **Criar um Novo Certificado**, clique no ícone de calendário e selecione uma **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar Logon Único](./media/active-directory-saas-firmplay-tutorial/tutorial_general_300.png)

6. Na seção **Certificado de Autenticação SAML**, selecione **Ativar o novo certificado** e clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_04.png)

7. Na janela pop-up **Certificado de substituição**, clique em **OK**.

    ![Configurar o logon único](./media/active-directory-saas-firmplay-tutorial/tutorial_general_400.png)

8. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador. 

    ![Configurar Logon Único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_05.png) 

9. Na seção **Configuração do FirmPlay - Employee Advocacy for Recruiting**, clique em **Configurar FirmPlay - Employee Advocacy for Recruiting** para abrir a caixa de diálogo **Configurar logon**.

    ![Configurar Logon Único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Configurar Logon Único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_07.png)

10. Para que o SSO seja configurado para seu aplicativo, entre em contato com a [equipe de suporte do FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) e forneça o seguinte: 

    • O **Arquivo de certificado** baixado

    • A **URL do Serviço de Logon Único SAML**

    • A **ID da entidade SAML**

    • A **URL de Saída**
  

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-firmplay-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-firmplay-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-firmplay-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-firmplay-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Criando um usuário de teste do FirmPlay - Employee Advocacy for Recruiting

Nesta seção, você irá criar um usuário denominado Brenda Fernandes no FirmPlay - Employee Advocacy for Recruiting. Trabalhe com a [ equipe de suporte do FirmPlay - Employee Advocacy for Recruiting](mailto:engineering@firmplay.com) para adicionar os usuários na plataforma do FirmPlay - Employee Advocacy for Recruiting.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao FirmPlay - Employee Advocacy for Recruiting.

![Atribuir usuário][200] 

**Para adicionar Brenda Fernandes ao FirmPlay - Employee Advocacy for Recruiting, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **Aplicativos empresariais**, depois clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **FirmPlay - Employee Advocacy for Recruiting**.

    ![Configurar o logon único](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    


### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco FirmPlay - Employee Advocacy for Recruiting no Painel de Acesso, deverá ser automaticamente conectado ao aplicativo FirmPlay - Employee Advocacy for Recruiting.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_203.png
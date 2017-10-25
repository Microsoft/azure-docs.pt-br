---
title: "Tutorial: Integração do Azure Active Directory com o @Task | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o @Task."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: ebb19ca6cbaf04106fbce937d95651e709854cfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>Tutorial: Integração do Active Directory do Azure com @Task
O objetivo desse tutorial é mostrar como integrar @Task ao Azure Active Directory (AD do Azure).  
A integração de @Task ao AD do Azure oferece os seguintes benefícios: 

* No AD do Azure, é possível controlar quem tem acesso a @Task
* Você pode permitir que seus usuários façam logon automaticamente em @Task (logon único) com suas contas do AD do Azure
* Você pode gerenciar suas contas em um único local: o portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o @Task, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único @Task

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em três blocos de construção principais:

1. Adicionar @Task da galeria 
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-task-from-the-gallery"></a>Adicionar @Task da galeria
Para configurar a integração de @Task ao AD do Azure, você precisa adicionar @Task por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar @Task por meio da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1] 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2] 
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3] 
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4] 
6. Na caixa de pesquisa, digite **@Task**.
   
    ![Aplicativos][5] 
7. No painel de resultados, selecione **@Task** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Aplicativos][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do AD do Azure com @Task, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o AD do Azure precisa saber qual usuário de @Task é equivalente a um usuário do AD do Azure. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado em @Task.   
Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no @Task.

Para configurar e testar o logon único do Azure AD com o @Task, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um @Tasktest usuário](#creating-a-halogen-software-test-user)** - para ter um equivalente de Brenda Fernandes em @Taskthat que esteja vinculado à representação dela no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do AD do Azure no portal clássico do Azure e configurar o logon único em seu aplicativo de @Task.

**Para configurar o logon único do Azure AD com o @Task, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração de aplicativos **@Task**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon em @Task**, selecione **Logon único do AD do Azure** e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][7] 
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Definir Configurações de Aplicativo][8] 
   
     a. Na caixa de texto **URL de Resposta**, digite a URL usada pelos usuários para entrar no site @Task (por exemplo: *https://<Tenant name>.attask-ondemand.com*).
   
     b. Clique em **Avançar**.
4. Na página **Configurar logon único em @Task**, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador e clique em **Avançar**.
   
    ![O que é o Azure AD Connect][9] 
5. Faça logon no site da empresa @Task como administrador.
6. Vá para **Configuração de Logon Único**.
7. Na caixa de diálogo **Logon Único** , realize as seguintes etapas
   
    ![Configurar Logon Único][23]
   
    a. Como **Tipo**, selecione **SAML 2.0**.
   
    b. Selecione **ID do Provedor de Serviços**.
   
    c. No portal clássico do Azure, copie a **URL de Logon Remoto** e cole-a na caixa de texto **URL do Portal de Logon**.
   
    d. No portal clássico do Azure, copie a **URL do Serviço de Logout Único** e cole-a na caixa de texto **URL de Logout**.
   
    e. No portal clássico do Azure, copie a **URL de Alteração de Senha** e cole-a na caixa de texto URL de **Alteração de Senha**.
   
    f. Clique em **Salvar**.
8. No portal clássico do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**. 
   
    ![O que é o Azure AD Connect][10]
9. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![O que é o Azure AD Connect][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Avançar**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
    a. Na caixa de texto **Nome**, digite **Brenda**.  
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Avançar**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-task-test-user"></a>Criando um usuário de teste de @Task
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no @Task.

**Para criar um usuário chamado Brenda Fernandes em @Task, execute as seguintes etapas:**

1. Faça logon no site da empresa @Task como administrador.
2. No menu na parte superior, clique em **Pessoas**.
3. Clique em **Nova Pessoa**. 
4. Na caixa de diálogo Nova Pessoa, execute as seguintes etapas:
   
    ![Criar um usuário de teste de @Task][21] 
   
    a. Na caixa de texto **Nome** , digite “Brenda”.
   
    b. Na caixa de texto **Sobrenome** , digite “Fernandes”.
   
    c. Na caixa de texto **Endereço de Email** , digite o endereço de email de Brenda Fernandes no Active Directory do Azure.
   
    d. Clique em **Adicionar Pessoa**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes para usar o logon único do Azure, concedendo a ela acesso ao @Task.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes a @Task, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **@Task**.
   
    ![Atribuir usuário][202] 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Ao clicar no bloco de @Task no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo de @Task.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png







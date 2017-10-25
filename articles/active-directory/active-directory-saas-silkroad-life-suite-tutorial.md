---
title: "Tutorial: Integração do Azure Active Directory com o SilkRoad Life Suite | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o SilkRoad Life Suite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
ms.openlocfilehash: ecf4e31ecea00d003fc47ea4cebb781ca58957f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: integração do Active Directory do Azure com o SilkRoad Life Suite
O objetivo desse tutorial é mostrar como integrar o SilkRoad Life Suite ao Azure AD (Azure Active Directory). 

A integração do SilkRoad Life Suite ao Azure AD oferece os seguintes benefícios: 

* Você pode controlar no AD do Azure quem tem acesso ao SilkRoad Life Suite 
* Você pode habilitar seus usuários a fazer logon automaticamente no SilkRoad Life Suite usando SSO (logon único) com suas contas do Azure AD

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure com o SilkRoad Life Suite, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do SilkRoad Life Suite habilitada para logon único

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o SilkRoad Life Suite por meio da galeria 
2. Configurar e testar o SSO do Azure AD

## <a name="add-silkroad-life-suite-from-the-gallery"></a>Adicionar o SilkRoad Life Suite da galeria
Para configurar a integração do SilkRoad Life Suite com o AD do Azure, você precisa adicionar o SilkRoad Life Suite por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SilkRoad Life Suite por meio da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **SilkRoad Life Suite**.
   
    ![Aplicativos][5]

7. No painel de resultados, selecione **SilkRoad Life Suite** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Aplicativos][50]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o SilkRoad Life Suite, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa saber qual usuário do SilkRoad Life Suite é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no SilkRoad Life Suite.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no SilkRoad Life Suite.

Para configurar e testar o logon único do AD do Azure com o SilkRoad Life Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do SilkRoad Life Suite](#creating-a-silkroad-life-suite-test-user)** - para ter um equivalente de Brenda Fernandes no SilkRoad Life Suite que esteja vinculado à representação dela no AD do Azure.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o SSO em seu aplicativo SilkRoad Life Suite.

**Para configurar o logon único do AD do Azure com o SilkRoad Life Suite, realize as seguintes etapas:**

1. Faça logon no site da empresa SilkRoad como administrador. 

  >[!NOTE] 
  > Para obter acesso ao aplicativo de Autenticação do SilkRoad Life Suite para configurar a federação com o AD do Microsoft Azure, entre em contato com o Suporte da SilkRoad ou com seu representante de Serviços da SilkRoad.
  > 

2. Vá para **Provedor de Serviços** e clique em **Detalhes de Federação**. 
   
    ![Logon Único do AD do Azure][10] 

3. Clique em **Baixar Metadados de Federação**e salve o arquivo de metadados no computador.
   
    ![Logon Único do AD do Azure][11] 

4. No portal clássico do Azure, na página de integração de aplicativos do **SilkRoad Life Suite**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar o logon único][6] 

5. Na página **Como você deseja que os usuários façam logon no SilkRoad Life Suite**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][7] 

6. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Logon Único do AD do Azure][8]   
 1. Na caixa de texto **URL de Entrada**, digite a URL usada pelos usuários para fazer logon em seu site do SilkRoad Life Suite (por ex.: *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).  
 2. Abra o arquivo de metadados do **Silkroad** baixado. 
 3. Localize a marca **AssertionConsumerService** e copie o atributo **Location**.         
   
    ![Logon Único do AD do Azure][21] 
 4. Cole o valor na caixa de texto **URL de Resposta** .  
 5. Clique em **Avançar**.

6. Na página **Configurar logon único no SilkRoad Life Suite** , execute as seguintes etapas:
   
    ![Logon Único do AD do Azure][9]  
 1. Clique em Baixar certificado e salve o certificado localmente no computador.  
 2. Clique em **Avançar**.

7. No aplicativo **SilkRoad**, clique em **Fontes de Autenticação**.
   
    ![Logon Único do AD do Azure][12] 

8. Clique em **Adicionar Fonte de Autenticação**. 
   
    ![Logon único do AD do Azure][13] 

9. Na seção **Adicionar Fonte de Autenticação** , execute as seguintes etapas: 
   
    ![Logon Único do AD do Azure][14]  
 1. Em **Opção 2 – Arquivo de Metadados**, clique em **Procurar** para carregar o arquivo de metadados baixado.  
 2. Clique em **Criar Provedor de Identidade usando Dados de Arquivo**.

10. Na seção **Fontes de Autenticação**, clique em **Editar**. 
    
     ![Logon Único do AD do Azure][15] 

11. No diálogo **Editar Fonte de Autenticação** , execute as seguintes etapas: 
    
     ![Logon Único do AD do Azure][16] 
 1. Para **Habilitado**, selecione **Sim**.   
 2. Na caixa de texto **Descrição do IdP** , digite uma descrição para a sua configuração (por exemplo: *SSO do Azure AD*).  
 3. Na caixa de texto **Nome do IdP** , digite um nome específico para a sua configuração (por exemplo: *Azure SP*).  
 4. Clique em **Salvar**.

12. Desabilite todas as outras fontes de autenticação. 
    
     ![Logon Único do AD do Azure][17]

13. No portal clássico do Azure, na página **Confirmação de logon único**, clique em **Avançar**.  
    
     ![Logon Único do AD do Azure][18]

14. Na página **Confirmação de logon único**, clique em **Concluir**.
    
     ![Logon Único do AD do Azure][19]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)  

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)  
 1. Em Tipo de Usuário, selecione Novo usuário na organização.  
 2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**. 
 3. Clique em **Avançar**.

6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)  
 1. Na caixa de texto **Nome**, digite **Brenda**.    
 2. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
 3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**. 
 4. Na lista **Função**, selecione **Usuário**.
 5. Clique em **Avançar**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)  
 1. Anote o valor da **Nova Senha**. 
 2. Clique em **Concluído**.   

### <a name="create-a-silkroad-life-suite-test-user"></a>Criar um usuário de teste do SilkRoad Life Suite
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no SilkRoad Life Suite. Brenda deve ter uma ID de SSO (às vezes chamada um *AuthParam*) que corresponde ao **emailaddress** de Brenda no Azure AD.

**Para criar um usuário chamado Brenda Fernandes no SilkRoad Life Suite, realize as seguintes etapas:**

- Peça à sua equipe de suporte do SilkRoad Life Suite para criar um usuário que tenha o atributo **ID de SSO** com o mesmo valor que **emailaddress** de Brenda Fernandes no Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao SilkRoad Life Suite.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao SilkRoad Life Suite, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SilkRoad Life Suite**.
   
    ![Atribuir usuário][202] 

3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  

Quando clica no bloco SilkRoad Life Suite no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo SilkRoad Life Suite.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png






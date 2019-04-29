---
title: 'Tutorial: Integração do Azure Active Directory com o Skillport | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Skillport.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4df349b2-a73f-4b88-a077-ec0fbfc26527
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 936ad2e49aaf449144296dd941a3fbc42719fe4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618111"
---
# <a name="tutorial-azure-active-directory-integration-with-skillport"></a>Tutorial: Integração do Azure Active Directory com o Skillport

Neste tutorial, você aprenderá a integrar o Skillport ao Azure AD (Azure Active Directory).

A integração do Skillport ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Skillport
- Você pode permitir que seus usuários façam logon automaticamente no Skillport (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Skillport, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Skillport com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Skillport da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-skillport-from-the-gallery"></a>Adicionando Skillport da galeria
Para configurar a integração do Skillport ao Azure AD, você precisará adicionar o Skillport da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Skillport por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
1. Clique no botão **Novo Aplicativo** na parte superior da caixa de diálogo.

    ![Aplicativos][3]

1. Na caixa de pesquisa, digite **Skillport**.

    ![Criação de um usuário de teste do AD do Azure](./media/skillport-tutorial/tutorial_skillport_search.png)

1. No painel de resultados, selecione **Skillport** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/skillport-tutorial/tutorial_skillport_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você vai configurar e testar o logon único do Azure AD com o Skillport com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Skillport é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Skillport.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Skillport.

Para configurar e testar o logon único do Azure AD com o Skillport, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criação de um usuário de teste do Skillport](#creating-a-skillport-test-user)** – para ter um equivalente de Brenda Fernandes no Skillport que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no portal do Azure e configurar o logon único em seu aplicativo Skillport.

**Para configurar o logon único do Azure AD com o Skillport, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Skillport**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/skillport-tutorial/tutorial_skillport_samlbase.png)

1. Na seção **URLs e Domínio do Skillport**, execute as seguintes etapas:

    ![Configurar o logon único](./media/skillport-tutorial/tutorial_skillport_url.png)

     a. Na caixa de texto **URL de Logon**, digite a URL:
      
      Datacenter da UE: `https://adfs.skillport.eu`
   
      Datacenter dos EUA: `https://sso.skillport.com`

    b. Na caixa de texto **Identificador**, digite a URL:
      
      Datacenter da UE: `http://adfs.skillport.eu/adfs/services/trust`
   
      Datacenter dos EUA: `https://sso.skillport.com`
   
    c. Na caixa de texto **URL de resposta**, digite a URL:
    
      Datacenter da UE: `https://adfs.skillport.eu/adfs/ls/`
    
      Datacenter dos EUA: `https://sso.skillport.com/sp/ACS.saml2`
 
1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar o logon único](./media/skillport-tutorial/tutorial_skillport_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/skillport-tutorial/tutorial_general_400.png)

1. Para configurar o logon único no lado do **Skillport**, é necessário enviar o **XML de metadados** baixado para a [equipe de suporte do Skillport](https://www.skillsoft.com/contact.asp). Eles configurarão isso para terem a conexão de SSO do SAML definida corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/skillport-tutorial/create_aaduser_01.png) 

1. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/skillport-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Adicionar** para abrir a caixa de diálogo **Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/skillport-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/skillport-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-skillport-test-user"></a>Criar um usuário de teste do Skillport

Para criar um usuário de teste do Skillport, você precisa entrar em contato com [a equipe de suporte do Skillport](https://www.skillsoft.com/contact.asp), pois eles têm vários cenários de negócios de acordo com o requisito de usuário final. Eles configurarão isso após conversar com os usuários.  

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Skillport.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Skillport, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Skillport**.

    ![Configurar o logon único](./media/skillport-tutorial/tutorial_skillport_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Skillport no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Skillport.
Para saber mais sobre o Painel de Acesso, confira  [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillport-tutorial/tutorial_general_01.png
[2]: ./media/skillport-tutorial/tutorial_general_02.png
[3]: ./media/skillport-tutorial/tutorial_general_03.png
[4]: ./media/skillport-tutorial/tutorial_general_04.png

[100]: ./media/skillport-tutorial/tutorial_general_100.png

[200]: ./media/skillport-tutorial/tutorial_general_200.png
[201]: ./media/skillport-tutorial/tutorial_general_201.png
[202]: ./media/skillport-tutorial/tutorial_general_202.png
[203]: ./media/skillport-tutorial/tutorial_general_203.png


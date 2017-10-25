---
title: "Tutorial: integração do Azure Active Directory com o AirWatch | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o AirWatch."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 1996ec97e7c0d94c5606ca43bb5956548f1f3712
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Tutorial: Integração do Active Directory do Azure ao AirWatch

Neste tutorial, você aprenderá a integrar o AirWatch ao Azure AD (Azure Active Directory).

A integração do AirWatch ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao AirWatch
- Você pode permitir que seus usuários façam logon automaticamente no AirWatch (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao AirWatch, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do AirWatch

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do AirWatch da Galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-airwatch-from-the-gallery"></a>Adição do AirWatch da Galeria
Para configurar a integração do AirWatch com o Azure AD, você precisa adicionar o AirWatch à sua lista de aplicativos SaaS gerenciados a partir da galeria.

**Para adicionar o AirWatch da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **AirWatch**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_search.png)

5. No painel de resultados, selecione **AirWatch** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o AirWatch, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do AirWatch é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado do AirWatch.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no AirWatch.

Para configurar e testar o logon único do Azure AD com o AirWatch, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do AirWatch](#creating-a-airwatch-test-user)** – para ter um equivalente de Brenda Fernandes no AirWatch que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo AirWatch.

**Para configurar o logon único do Azure AD com o AirWatch, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **AirWatch**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. Na seção **Domínio e URLs do AirWatch**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. Na caixa de texto **Identificador**, digite o valor como `AirWatch`

    > [!NOTE] 
    > Esse valor não é o real. Atualize esse valor com a URL de Entrada real. Para obter esse valor, entre em contato com a [equipe de suporte do cliente AirWatch](http://www.air-watch.com/company/contact-us/). 
 
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. Na seção **Configuração do AirWatch**, clique em **Configurar o AirWatch** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-airwatch-tutorial/tutorial_general_400.png)
<CS>
7. Em outra janela do navegador da Web, faça logon em seu site de empresa AirWatch como um administrador.

8. No painel de navegação à esquerda, clique em **Contas** e em **Administradores**.
   
   ![Administradores](./media/active-directory-saas-airwatch-tutorial/ic791920.png "Administradores")

9. Expanda o menu **Configurações** e clique em **Serviços de Diretório**.
   
   ![Configurações](./media/active-directory-saas-airwatch-tutorial/ic791921.png "Configurações")

10. Clique na guia **Usuário**, na caixa de texto **DN Base**, digite seu nome de domínio e clique em **Salvar**.
   
   ![Usuário](./media/active-directory-saas-airwatch-tutorial/ic791922.png "Usuário")

11. Clique na guia **Servidor** .
   
   ![Servidor](./media/active-directory-saas-airwatch-tutorial/ic791923.png "Servidor")

12. Execute as seguintes etapas:
    
    ![Upload](./media/active-directory-saas-airwatch-tutorial/ic791924.png "Upload")   
    
    a. Para **Tipo de Diretório**, selecione **Nenhum**.

    b. Selecione **Usar SAML para Autenticação**.

    c. Para carregar o certificado baixado, clique em **Carregar**.

13. Na seção **Solicitar** , realize as seguintes etapas:
    
    ![Solicitação](./media/active-directory-saas-airwatch-tutorial/ic791925.png "Solicitação")  

    a. Para **Tipo de Associação de Solicitação**, selecione **POST**.

    b. No Portal do Azure, na página do diálogo **Configurar logon único no AirWatch**, copie o valor da **URL do Serviço de Logon Único SAML** e cole-o na caixa de texto **URL de Entrada Único do Provedor de Identidade**.

    c. Para **Formato de NameID**, selecione **Endereço de Email**.

    d. Clique em **Salvar**.

14. Clique na guia **Usuário** novamente.
    
    ![Usuário](./media/active-directory-saas-airwatch-tutorial/ic791926.png "Usuário")

15. Na seção **Atributo** , realize as seguintes etapas:
    
    ![Atributo](./media/active-directory-saas-airwatch-tutorial/ic791927.png "Atributo")

    a. Na caixa de texto **Identificador de Objeto**, digite **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. Na caixa de texto **Nome de Usuário**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. Na caixa de texto **Nome de Exibição**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. Na caixa de texto **Nome**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. Na caixa de texto **Sobrenome**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. Na caixa de texto **Email**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Clique em **Salvar**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-airwatch-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-airwatch-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-airwatch-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-airwatch-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-airwatch-test-user"></a>Criando um usuário de teste do AirWatch

Para permitir que os usuários do Azure AD façam logon no AirWatch, eles devem ser provisionados no AirWatch.

* No caso do AirWatch, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **AirWatch** como administrador.
2. No painel de navegação à esquerda, clique em **Contas** e em **Usuários**.
   
   ![Usuários](./media/active-directory-saas-airwatch-tutorial/ic791929.png "Usuários")
3. No menu **Usuários**, clique em **Exibição de Lista** e em **Adicionar \> Adicionar Usuário**.
   
   ![Adicionar Usuário](./media/active-directory-saas-airwatch-tutorial/ic791930.png "Adicionar Usuário")
4. No diálogo **Adicionar/Editar Usuário** , realize as seguintes etapas:

   ![Adicionar Usuário](./media/active-directory-saas-airwatch-tutorial/ic791931.png "Adicionar Usuário")   
   1. Digite **Nome**, **Senha**, **Confirmar Senha**, **Nome**, **Sobrenome** e **Endereço de Email** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.
   2. Clique em **Salvar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do AirWatch ou as APIs fornecidas pelo AirWatch para provisionar as contas de usuário do AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao AirWatch.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao AirWatch, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **AirWatch**.

    ![Configurar Logon Único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_203.png


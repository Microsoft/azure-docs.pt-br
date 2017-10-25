---
title: "Tutorial: integração do Azure Active Directory com o Adaptive Suite | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Adaptive Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 5d7ba2f4c7d814e3aaa1bf804ddc5030380ccb2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Tutorial: Integração do Active Directory do Azure ao Adaptive Suite

Neste tutorial, você aprenderá a integrar o Adaptive Suite ao Azure AD (Azure Active Directory).

A integração do Adaptive Suite ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Adaptive Suite
- Você pode habilitar seus usuários a fazer logon automaticamente no Adaptive Suite (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Adaptive Suite, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Adaptive Suite

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Adaptive Suite da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-adaptive-suite-from-the-gallery"></a>Adicionando o Adaptive Suite da galeria
Para configurar a integração do Adaptive Suite com o Azure AD, você precisa adicionar o Adaptive Suite por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adaptive Suite por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Adaptive Suite**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. No painel de resultados, selecione **Adaptive Suite** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Adaptive Suite, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Adaptive Suite é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Adaptive Suite.

No Adaptive Suite, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Adaptive Suite, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Adaptive Suite](#creating-an-adaptive-suite-test-user)** – para ter um equivalente de Brenda Fernandes no Adaptive Suite que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo Adaptive Suite.

**Para configurar o logon único do Azure AD com o Adaptive Suite, realize as seguintes etapas:**

1. No Portal do Azure, na página de integração do aplicativo **Adaptive Suite**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. Na seção **URLs e Domínio do Adaptive Suite**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Você pode obter esse valor na página **Configurações de SSO do SAML** do Adaptive Suite.
    >  

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Adaptive Suite**, clique em **Configurar o Adaptive Suite** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. Em outra janela do navegador da Web, faça logon em seu site de empresa Adaptive Suite como administrador.

8. Vá para **Administrador**.
   
    ![Admin](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Admin")

9. Na seção **Usuários e Funções**, clique em **Gerenciar Configurações de SSO do SAML**.
   
    ![Gerenciar Configurações de SSO do SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "Gerenciar Configurações de SSO do SAML")

10. Na página **Configurações de SSO do SAML** , realize as seguintes etapas:
   
    ![Configurações de SSO do SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "Configurações de SSO do SAML")

    a. Na caixa de texto **Nome do provedor de identidade** , digite um nome para a sua configuração.
    
    b. Cole a **ID da Entidade SAML** copiada do Portal do Azure na caixa de texto **ID da entidade do provedor de identidade**.
  
    c. Cole o valor da **URL de Entrada do Provedor de Identidade** copiado do Portal do Azure na caixa de texto **URL de SSO do Provedor de Identidade**.
  
    d. Cole o valor da **URL de Entrada do Provedor de Identidade** copiado do Portal do Azure na caixa de texto **URL de Logoff Personalizado**.
  
    e. Para carregar seu certificado baixado, clique em **Escolher arquivo**.
  
    f. Selecione as seguintes opções para:
    * **ID de usuário do SAML**, selecione **Nome de usuário do Adaptive Insights do Usuário**.
    * **Local da ID de usuário do SAML**, selecione **ID de usuário em NameID da Entidade**.
    * **Formato de NameID do SAML**, selecione **Endereço de email**.
    * **Habilitar SAML**, selecione **Permitir SSO do SAML e direcionar logon do Adaptive Insights**.
    
    g. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-adaptive-suite-test-user"></a>Criando de um usuário de teste do Adaptive Suite

Para permitir que os usuários do Azure AD façam logon no Adaptive Suite, eles devem ser provisionados no Adaptive Suite.  

* No caso do Adaptive Suite, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:** 

1. Faça logon em seu site de empresa do **Adaptive Suite** como administrador.
2. Vá para **Administrador**.
   
   ![Admin](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Admin")
3. Na seção **Usuários e Funções**, clique em **Adicionar Usuário**.
   
   ![Adicionar Usuário](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "Adicionar Usuário")
4. Na seção **Novo Usuário** , realize as seguintes etapas:
   
   ![Enviar](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "Enviar")   

   a. Digite o **Nome**, **Logon**, **Email** e **Senha** de um usuário válido do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.
  
   b. Selecione uma **Função**.
  
   c. Clique em **Enviar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Adaptive Suite ou as APIs fornecidas pelo Adaptive Suite para provisionar as contas de usuário do AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Adaptive Suite.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Adaptive Suite, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Adaptive Suite**.

    ![Configurar Logon Único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de logon único do Microsoft Azure AD usando o Painel de Acesso.

Quando clica no bloco Adaptive Suite no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo Adaptive Suite.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png


---
title: "Tutorial: integração do Azure Active Directory com o Igloo Software | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Igloo Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: ab3891e11eb33b4d233e4fc967a40c7df06e4f4e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: integração do Active Directory do Azure ao Igloo Software

Neste tutorial, você aprenderá a integrar o Igloo Software ao Azure AD (Azure Active Directory).

A integração do Igloo Software ao Azure AD oferece os seguintes benefícios:

- Você pode controlar, no Azure AD, quem tem acesso ao Igloo Software
- Você pode habilitar seus usuários a fazer logon automaticamente no Igloo Software (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Igloo Software, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Igloo Software habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Igloo Software da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-igloo-software-from-the-gallery"></a>Adicionando Igloo Software da galeria
Para configurar a integração do Igloo Software ao Azure AD, você precisa adicionar o Igloo Software da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Igloo Software da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Igloo Software**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. No painel de resultados, escolha **Igloo Software** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Igloo Software com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Igloo Software é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Igloo Software.

No Igloo Software, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Igloo Software, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Como criar um usuário de teste do Igloo Software](#creating-an-igloo-software-test-user)** – para ter um equivalente de Brenda Fernandes no Igloo Software que esteja vinculado à representação do usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo do Igloo Software.

**Para configurar o logon único do Azure AD com o Igloo Software, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativo **Igloo Software**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. Na seção **URLs e Domínio do Igloo Software**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.igloocommmunities.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<company name>.igloocommmunities.com/saml.digest`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Entre em contato com a [equipe de suporte ao Cliente do Igloo Software](https://www.igloosoftware.com/services/support) para obter esses valores. 

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-igloo-software-tutorial/tutorial_general_400.png)
    
6. Na seção **Configuração do Igloo Software**, clique em **Configurar Igloo Software** para abrir a janela **Configurar logon**. Copie a **URL do serviço de logon único do SAML e a URL de logoff** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Igloo Software como administrador.

8. Vá para o **Painel de Controle**.
   
     ![Painel de controle](./media/active-directory-saas-igloo-software-tutorial/ic799949.png "Painel de controle")

9. Na guia **Associação**, clique em **Configurações de Entrada**.
   
    ![Configurações de entrada](./media/active-directory-saas-igloo-software-tutorial/ic783968.png "Configurações de entrada")

10. Na seção Configuração do SAML, clique em **Configurar Autenticação SAML**.
   
    ![Configuração SAML](./media/active-directory-saas-igloo-software-tutorial/ic783969.png "configuração SAML")
   
11. Na seção **Configuração Geral** , realize as seguintes etapas:
   
    ![Configuração geral](./media/active-directory-saas-igloo-software-tutorial/ic783970.png "Configuração geral")

    a. Na caixa de texto **Nome da Conexão** , digite um nome personalizado para a sua configuração.
   
    b. Na caixa de texto **URL de Logon do IdP**, cole o valor da **URL de Serviço de Logon Único do SAML** copiado do Portal do Azure.
   
    c. Na caixa de texto **URL de Logoff do IdP**, cole o valor da **URL de Saída** copiado do portal do Azure.
    
    d. Selecione **Tipo HTTP de Solicitação e Resposta de Logoff** como **POST**.
   
    e. Abra seu certificado codificado em **base-64** no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado Público**.
    
12. Em **Configuração de Resposta e Autenticação**, realize as seguintes etapas:
    
    ![Configuração de autenticação e resposta](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Configuração de autenticação e resposta")
  
      a. Para **Provedor de Identidade**, selecione **Microsoft ADFS**.
      
      b. Para **Tipo de Identificador**, selecione **Endereço de Email**. 

      c. Na caixa de texto **Atributo de Email**, digite **emailaddress**.

      d. Na caixa de texto **Atributo de Nome**, digite **givenname**.

      e. Na caixa de texto **Atributo de Sobrenome**, digite **surname**.

13. Execute as seguintes etapas para concluir a configuração:
    
    ![Criação de usuário na entrada](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Criação de usuário na entrada") 

     a. Para **Criação de usuário na Entrada**, selecione **Criar um novo usuário em seu site ao entrar**.

     b. Para **Configurações de Entrada**, selecione **Usar botão do SAML na tela “Entrar”**.

     c. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-an-igloo-software-test-user"></a>Como criar um usuário de teste do Igloo Software

Não há item de ação para a configuração do provisionamento de usuário para o Igloo Software.  

Quando um usuário atribuído tenta fazer logon no Igloo Software usando o painel de acesso, o Igloo Software verifica se o usuário existe.  Se ainda não houver conta de usuário, ela será criada automaticamente pelo Igloo Software.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao Igloo Software.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Igloo Software, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Igloo Software**.

    ![Configurar Logon Único](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Igloo Software no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Igloo Software.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_203.png


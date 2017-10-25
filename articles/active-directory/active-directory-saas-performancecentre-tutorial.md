---
title: "Tutorial: integração do Azure Active Directory ao PerformanceCentre | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o PerformanceCentre."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: e86adaf4bd9b4752f2aece8207a8a423ec5590a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Tutorial: Integração do Active Directory do Azure com o PerformanceCentre

Neste tutorial, você aprenderá a integrar o PerformanceCentre ao Azure AD (Azure Active Directory).

A integração do PerformanceCentre ao Azure AD proporciona os seguintes benefícios:

- No AD do Azure, você pode controlar quem tem acesso ao PerformanceCentre
- Você pode permitir que seus usuários façam logon automaticamente no PerformanceCentre (logon único) com suas contas do AD do Azure
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do AD do Azure com o PerformanceCentre, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do PerformanceCentre com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o PerformanceCentre da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-performancecentre-from-the-gallery"></a>Adicionando o PerformanceCentre da galeria
Para configurar a integração do PerformanceCentre ao AD do Azure, você precisará adicionar o PerformanceCentre da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o PerformanceCentre da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **PerformanceCentre**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_search.png)

5. No painel de resultados, selecione **PerformanceCentre** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o PerformanceCentre, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do PerformanceCentre é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PerformanceCentre.

No PerformanceCentre, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do AD do Azure com o PerformanceCentre, você precisará concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do PerformanceCentre](#creating-a-performancecentre-test-user)** – para ter um equivalente de Brenda Fernandes no PerformanceCentre que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único em seu aplicativo PerformanceCentre.

**Para configurar o logon único do AD do Azure com o PerformanceCentre, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **PerformanceCentre**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

3. Na seção **URLs e Domínio do PerformanceCentre**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `http://companyname.performancecentre.com/saml/SSO`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do PerformanceCentre](https://www.performancecentre.com/contact-us/) para obter esses valores. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-performancecentre-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do PerformanceCentre**, clique em **Configurar o PerformanceCentre** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único do SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_configure.png) 

7. Faça logon no site da empresa **PerformanceCentre** como administrador.

8. Na guia à esquerda, clique em **Configurar**.
   
    ![Logon Único do AD do Azure][10]

9. Na guia à esquerda, clique em **Diversos** e em **Logon Único**.
   
    ![Logon Único do AD do Azure][11]

10. Para o **Protocolo**, escolha **SAML**.
   
    ![Logon Único do AD do Azure][12]

11. Abra o arquivo de metadados baixado no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Metadados do Provedor de Identidade** e clique em **Salvar**.
   
    ![Logon Único do AD do Azure][13]

12. Verifique se os valores para **URL Base da Entidade** e **URL da ID da Entidade** estão corretos.
    
     ![Logon Único do AD do Azure][14]

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-performancecentre-test-user"></a>Criar um usuário de teste do PerformanceCentre

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no PerformanceCentre.

**Para criar um usuário chamado Brenda Fernandes no PerformanceCentre, execute as seguintes etapas:**

1. Faça logon no site da empresa PerformanceCentre como administrador.

2. No menu à esquerda, clique em **Inter-relacionado**, e clique em **Criar Participante**.
   
    ![Criar Usuário][400]

3. No diálogo **Inter-relacionado – Criar Participante** , execute as seguintes etapas:
   
    ![Criar Usuário][401]
    
    a. Digite os atributos necessários para Brenda Fernandes nas caixas de texto relacionadas.
    
    >[!IMPORTANT]
    >O atributo Nome de Usuário de Brenda no PerformanceCentre deve ser igual ao Nome de Usuário no AD do Azure.
    
    b. Selecione **Administrador Cliente** como **Escolher Função**.
    
    c. Clique em **Salvar**. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao PerformanceCentre.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao PerformanceCentre, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **PerformanceCentre**.

    ![Configurar Logon Único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  

Quando clica no bloco PerformanceCentre no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo do PerformanceCentre.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png


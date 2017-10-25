---
title: "Tutorial: Integração do Azure Active Directory com o TeamSeer | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o TeamSeer."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 2a5e8f6d1443681c43db95da5cef0b7f2ef92291
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutorial: Integração do Active Directory do Azure ao TeamSeer

Neste tutorial, você aprenderá a integrar o TeamSeer ao Azure AD (Azure Active Directory).

A integração do TeamSeer ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao TeamSeer
- Você pode permitir que seus usuários façam logon automaticamente no TeamSeer (logon único) com as contas do Azure AD deles
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TeamSeer, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do TeamSeer

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o TeamSeer da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-teamseer-from-the-gallery"></a>Adicionar o TeamSeer da galeria
Para configurar a integração do TeamSeer ao Azure AD, você precisará adicionar o TeamSeer por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TeamSeer da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **TeamSeer**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_search.png)

5. No painel de resultados, selecione **TeamSeer** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o TeamSeer, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do TeamSeer é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TeamSeer.

No TeamSeer, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o TeamSeer, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do TeamSeer](#creating-a-teamseer-test-user)** – para ter um equivalente de Brenda Fernandes no TeamSeer que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo TeamSeer.

**Para configurar o logon único do Azure AD com o TeamSeer, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **TeamSeer**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_samlbase.png)

3. Na seção **URLs e Domínio do TeamSeer**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_url.png)

     Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do TeamSeer](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) para obter o valor. 
 
4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-teamseer-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do TeamSeer**, clique em **Configurar o TeamSeer** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_configure.png)

7. Em outra janela do navegador da Web, faça logon em seu site de empresa TeamSeer como um administrador.

8. Vá para **Administrador de RH**.
   
    ![Administrador de RH](./media/active-directory-saas-teamseer-tutorial/ic789634.png "Administrador de RH")

9. Clique em **Instalação**.
   
    ![Configuração](./media/active-directory-saas-teamseer-tutorial/ic789635.png "Configuração")

10. Clique em **Configurar detalhes do provedor de SAML**.
   
    ![Configurações do SAML](./media/active-directory-saas-teamseer-tutorial/ic789636.png "Configurações do SAML")

11. Na seção de detalhes do provedor de SAML, execute as seguintes etapas:
   
    ![Configurações do SAML](./media/active-directory-saas-teamseer-tutorial/ic789637.png "Configurações do SAML")   

    a. Cole o valor da **URL do Serviço de Logon Único** na caixa de texto **URL**.
          
    b. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado Público do IdP**.

12. Para concluir a configuração do provedor de SAML, execute as seguintes etapas:
    
    ![Configurações do SAML](./media/active-directory-saas-teamseer-tutorial/ic789638.png "Configurações do SAML") 

    a. Nos **Endereços de Email de Teste**, digite o endereço de email do usuário de teste. 
  
    b. Na caixa de texto **Emissor** , digite a URL do Emissor do provedor de serviços. 
  
    c. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamseer-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamseer-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamseer-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamseer-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-teamseer-test-user"></a>Criar um usuário de teste do TeamSeer

Para permitir que os usuários do Azure AD façam logon no TeamSeer, eles deverão ser provisionados no ShiftPlanning. No caso do TeamSeer, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **TeamSeer** como administrador.

2. Execute as seguintes etapas:
   
    ![Administrador de RH](./media/active-directory-saas-teamseer-tutorial/ic789640.png "Administrador de RH")  
 
    a. Vá para **Administrador de RH \> Usuários**.
  
    b. Clique em **Executar o assistente de Novo Usuário**.

3. Na seção **Detalhes do Usuário** , realize as seguintes etapas:
   
    ![Detalhes do Usuário](./media/active-directory-saas-teamseer-tutorial/ic789641.png "Detalhes do Usuário")

    a. Digite o **Nome**, **Sobrenome** e **Nome de usuário (Endereço de email)** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.
  
    b. Clique em **Avançar**.

4. Siga as instruções na tela para adicionar um novo usuário e clique em **Concluir**.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação da conta de usuário do TeamSeer ou APIs fornecidas pelo TeamSeer para provisionar as contas de usuário do AD do Azure. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao TeamSeer.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao TeamSeer, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **TeamSeer**.

    ![Configurar Logon Único](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_203.png

